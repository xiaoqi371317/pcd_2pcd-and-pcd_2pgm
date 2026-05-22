# ROS 2 Nav Studio: PCD Map Editor & Planner

A lightweight browser-based tool for ROS 2 map preparation, raw PCD visualization, point cloud origin resetting, occupancy map editing, and waypoint planning.

The project is implemented as a single-page HTML application using Three.js and HTML5 Canvas. It is designed for robotics workflows where a LiDAR-generated `.pcd` map needs to be inspected, transformed, converted into a 2D occupancy map, edited, and used for navigation planning.

## Description

ROS 2 Nav Studio provides an interactive workflow for:

- Viewing raw `.pcd` point clouds in 3D
- Adjusting LiDAR pose using translation and Roll/Pitch/Yaw parameters
- Picking a new target start pose directly inside the point cloud
- Exporting transformed PCD files for relocalization or map origin reset
- Generating ROS-compatible occupancy maps from PCD data
- Editing existing `PGM + YAML` maps
- Creating direction-aware waypoint routes for patrol/navigation tasks

It runs fully in the browser. No backend server is required.

## Screenshots

> Put your screenshots under `docs/images/` and update the paths if needed.

![Raw PCD viewer with configurable grid](docs/images/raw-pcd-viewer-grid.png)

![Interactive target start pose picker](docs/images/target-start-pose-picker.png)

![Waypoint planner](docs/images/waypoint-planner.png)

![Occupancy map editor](docs/images/map-editor.png)

## Main Features

### 1. Raw PCD Viewer

The raw point cloud viewer displays the original `.pcd` file without applying map-generation transforms. It is useful for checking the original LiDAR map structure before conversion or export.

Features:

- Load and render `.pcd` point clouds in 3D
- Orbit camera control
- Adjustable point size
- View reset / camera recentering
- ROS-style 3D coordinate visualization
- Red, green, and blue pose axes
- Axis labels:
  - X axis / Roll
  - Y axis / Pitch
  - Z axis / Yaw
- Configurable grid base:
  - Total grid size
  - Grid cell size
  - Center-line color
  - Normal grid-line color

### 2. LiDAR Initial Pose Visualization

The viewer can display the LiDAR initial pose using:

- `Tx`, `Ty`, `Tz`
- `Roll`, `Pitch`, `Yaw`

These parameters are shared with the PCD-to-map generation workflow.

The application also supports reading the PCD `VIEWPOINT` field and filling the pose parameters automatically when available.

### 3. Target Start Pose Picker

This feature is designed for relocalization and map origin resetting.

After loading the raw PCD file, the user can create a second interactive pose marker inside the point cloud. This target pose can be moved and rotated directly in 3D.

Supported operations:

- Create / show target start pose
- Translate the target pose in world space
- Rotate the target pose in local space
- Copy pose from the current LiDAR initial pose
- Move pose to the point cloud center
- Copy generated target pose parameters
- Clear the target pose
- Store and restore the target pose with browser `localStorage`

Once confirmed, the target pose is automatically written into the PCD transform export panel as the new `Start` pose and `Roll/Pitch/Yaw` parameters.

### 4. PCD Transform Export

The tool can export a new transformed `.pcd` file directly from the browser.

Supported PCD data formats:

- `ascii`
- `binary`

Unsupported:

- `binary_compressed`

Two transform modes are available.

#### Reset Start Pose Mode

This mode treats a selected pose inside the old PCD map as the origin of the new exported PCD coordinate frame.

Formula:

```text
P_out = R_start^T * (P_in - Start) + Out
```

Typical use cases:

- Resetting the map start point
- Preparing a point cloud for relocalization
- Simulating mapping from a different initial pose
- Normalizing a PCD coordinate frame

#### Direct Rigid Transform Mode

This mode rotates the whole PCD around a selected center and then applies a translation.

Formula:

```text
P_out = R * (P_in - Center) + Center + T
```

Typical use cases:

- Manual point cloud alignment
- Coordinate-frame correction
- Translating or rotating an existing PCD map

The exporter can optionally rewrite the PCD `VIEWPOINT` field to identity:

```text
VIEWPOINT 0 0 0 1 0 0 0
```

### 5. PCD to Occupancy Map Generation

The map editor can generate a 2D occupancy grid from a `.pcd` file.

Parameters:

- Resolution
- Min Z
- Max Z
- LiDAR translation
- LiDAR Roll/Pitch/Yaw
- Optional PCD `VIEWPOINT` auto-read

The PCD points are transformed into the world frame, filtered by Z height, and rasterized into a 2D occupancy image.

### 6. Occupancy Map Editor

The editor supports both generated maps and existing map assets.

Supported input:

- `.pcd`
- `.yaml`
- `.pgm`
- `.png`
- `.jpg`

Editing tools:

- Pan / move canvas
- Draw obstacles / add walls
- Erase to free space
- Adjustable brush size
- Map resolution calibration
- Origin X/Y calibration
- Optional Y-axis flip

Exported files:

- `map.pgm`
- `map.yaml`

The exported YAML uses ROS-compatible occupancy map settings:

```yaml
image: map.pgm
mode: trinary
resolution: <resolution>
origin: [<origin_x>, <origin_y>, 0.0]
negate: 0
occupied_thresh: 0.65
free_thresh: 0.196
```

### 7. Waypoint Planner

The route planner uses the current map as its background and lets the user create direction-aware waypoints.

Controls:

- Left mouse button: set waypoint position and drag to define yaw
- Right mouse button: pan map
- Mouse wheel: zoom

Waypoint output format:

```yaml
patrol_sender:
  ros__parameters:
    num_waypoints: 3
    # Format: [x, y, yaw(rad)]
    waypoint_0: [1.00, 2.00, 0.00]
    waypoint_1: [3.00, 4.00, 1.57]
    waypoint_2: [5.00, 6.00, -1.57]
```

This output can be copied directly from the UI.

## Coordinate Conventions

The application uses a ROS-style Roll/Pitch/Yaw convention:

```text
R = Rz(yaw) * Ry(pitch) * Rx(roll)
```

For the raw 3D viewer:

- X axis is shown in red
- Y axis is shown in green
- Z axis is shown in blue
- Z is treated as the vertical axis
- The grid is displayed on the XY plane

For the 2D map editor:

- Map coordinates are computed from `origin` and `resolution`
- The origin marker is drawn on the canvas
- Waypoint yaw is exported in radians

## Quick Start

### Option 1: Open Directly

Open the HTML file in a modern browser:

```text
ros2_nav_studio_pcd_transform_export_target_pose_picker.html
```

### Option 2: Serve Locally

If your browser blocks local module imports, serve the folder with a simple local server:

```bash
python3 -m http.server 8000
```

Then open:

```text
http://localhost:8000/ros2_nav_studio_pcd_transform_export_target_pose_picker.html
```

## Typical Workflow

### A. Generate a Map from PCD

1. Open the `Map Editor` tab.
2. Select a `.pcd` file.
3. Set `Resolution`, `Min Z`, and `Max Z`.
4. Set LiDAR `Tx/Ty/Tz/Roll/Pitch/Yaw`, or enable `VIEWPOINT` auto-read.
5. Click `Generate Map`.
6. Edit the generated occupancy map if required.
7. Export `map.pgm` and `map.yaml`.

### B. Reset PCD Start Pose for Relocalization

1. Open the `Raw PCD` tab.
2. Load / refresh the original point cloud.
3. Create the target start pose.
4. Use translation or rotation mode to adjust the pose.
5. Apply the pose to the reset-start parameters.
6. Return to the map editor export panel.
7. Export the transformed PCD.

### C. Create Waypoints

1. Load or generate a map.
2. Open the `Path Planner` tab.
3. Click and drag to create each waypoint and its yaw direction.
4. Copy the generated YAML parameters.

## Dependencies

The application imports Three.js modules from CDN:

- `three`
- `PCDLoader`
- `OrbitControls`
- `TransformControls`

No Node.js build step is required.

## Browser Requirements

Recommended:

- Chrome / Chromium-based browser
- Firefox
- Edge

The browser must support:

- ES modules
- Import maps
- File API
- WebGL
- HTML5 Canvas

## Limitations

- `binary_compressed` PCD export is not supported.
- Very large PCD files may require significant browser memory.
- The target start pose picker is a manual interactive tool, not an automatic ICP/NDT registration module.
- Internet access is required unless Three.js dependencies are vendored locally.
- Exported maps should still be validated in the target ROS 2 / Nav2 environment.

## Suggested Repository Structure

```text
.
├── README.md
├── ros2_nav_studio_pcd_transform_export_target_pose_picker.html
└── docs/
    └── images/
        ├── raw-pcd-viewer-grid.png
        ├── target-start-pose-picker.png
        ├── waypoint-planner.png
        └── map-editor.png
```

## Project Status

This project is currently a browser-based map preparation and visualization utility. It focuses on practical manual preprocessing for ROS 2 navigation workflows, especially PCD origin resetting and occupancy map editing.

## License

MIT License
