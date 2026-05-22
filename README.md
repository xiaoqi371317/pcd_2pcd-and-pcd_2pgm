# ROS 2 Nav Studio: PCD Map Editor & Planner

A lightweight browser-based toolkit for ROS 2 map preprocessing, raw PCD visualization, relocalization map origin resetting, occupancy grid editing, and waypoint planning.

![Raw PCD Viewer](docs/images/b7a8ebb9c22ecb715be3b7ffcde608ae.png)

## Features

### Raw PCD Viewer

- Native `.pcd` point cloud visualization
- Adjustable point size
- Interactive orbit camera
- Configurable grid size and colors
- Roll / Pitch / Yaw axis labels
- LiDAR pose visualization

![Target Pose Picker](docs/images/38e1dfe8902e0b316a81d5bc3418283d.png)

---

### Interactive Relocalization Pose Picker

Directly place and adjust a new relocalization start pose inside the point cloud.

- Interactive translation and rotation gizmo
- Real-time Start XYZ and RPY generation
- One-click synchronization to export parameters
- Local pose parameter persistence

Reset-start transform:

```text
P_out = R_start^T · (P_in - Start) + Out
```

Use cases:

- LiDAR relocalization
- Map origin resetting
- Multi-session map alignment
- SLAM coordinate normalization

---

### PCD Rigid Transform Export

Supported PCD data modes:

- `DATA ascii`
- `DATA binary`

Transform modes:

- Reset Start Pose
- Direct Rigid Transform

Functions:

- Rotation
- Translation
- Coordinate frame reset
- VIEWPOINT rewriting

---

### Occupancy Grid Map Editor

Generate and edit ROS-compatible occupancy maps.

Functions:

- Generate map from PCD
- Load existing `PGM + YAML`
- Draw / erase obstacles
- Adjust resolution and origin
- Export ROS-compatible maps

![Map Editor](docs/images/c9844c818346847ff4e86d2f63329cba.png)

---

### Waypoint Planner

Create direction-aware patrol/navigation waypoints.

Functions:

- Click-to-place waypoint
- Drag-to-set heading
- Automatic yaw calculation
- ROS parameter export

![Waypoint Planner](docs/images/4a815926f54a2ffbac1e765ade02178e.png)

---

## Quick Start

Clone the repository:

```bash
git clone <your-repo>
```

Open the HTML file directly in a browser:

```text
ros2_nav_studio_pcd_transform_export_target_pose_picker.html
```

Or start a local HTTP server:

```bash
python3 -m http.server 8000
```

Then visit:

```text
http://localhost:8000
```

---

## Recommended Workflow

### Reset PCD Start Pose

1. Open the web tool.
2. Select a `.pcd` file in the map editor panel.
3. Switch to the raw PCD viewer.
4. Load or refresh the raw point cloud.
5. Create or show the target start pose.
6. Move and rotate the target pose in the 3D viewer.
7. Apply the target pose to the reset-start export panel.
8. Export the transformed PCD file.

### Generate a 2D Occupancy Map from PCD

1. Select a `.pcd` file.
2. Set map resolution.
3. Set the `Min Z` and `Max Z` height filter.
4. Adjust LiDAR pose parameters if required.
5. Generate the map.
6. Edit the map if needed.
7. Export `PGM + YAML`.

### Edit an Existing Map

1. Select the `.yaml` file.
2. Select the corresponding `.pgm`, `.png`, or `.jpg` map image.
3. Load the map.
4. Edit obstacles using draw / erase tools.
5. Check resolution and origin.
6. Export the updated map.

### Create Waypoints

1. Switch to the waypoint planner.
2. Click to place a waypoint.
3. Drag to set heading.
4. Repeat for additional waypoints.
5. Copy the exported ROS parameter text.

---

## Supported Formats

### Input

- `.pcd`
- `.yaml`
- `.pgm`
- `.png`
- `.jpg`

### Output

- Transformed `.pcd`
- ROS occupancy map `.pgm`
- ROS map metadata `.yaml`
- Waypoint parameter text

---

## Coordinate Convention

The tool uses the common ROS roll-pitch-yaw order:

```text
R = Rz(yaw) * Ry(pitch) * Rx(roll)
```

Axis colors in the 3D viewer:

- Red: X axis / Roll direction
- Green: Y axis / Pitch direction
- Blue: Z axis / Yaw direction

---

## PCD Support

Supported:

- `DATA ascii`
- `DATA binary`

Not supported yet:

- `DATA binary_compressed`

If your PCD file uses `binary_compressed`, convert it to ASCII or binary PCD first.

---

## Suggested Repository Structure

```text
.
├── README.md
├── README_zh.md
├── ros2_nav_studio_pcd_transform_export_target_pose_picker.html
└── docs
    └── images
        ├── 38e1dfe8902e0b316a81d5bc3418283d.png
        ├── 4a815926f54a2ffbac1e765ade02178e.png
        ├── b7a8ebb9c22ecb715be3b7ffcde608ae.png
        └── c9844c818346847ff4e86d2f63329cba.png
```

The image paths in this README match the current `docs/images` filenames shown in the repository.

---

## Tech Stack

- Three.js
- PCDLoader
- OrbitControls
- HTML5 Canvas
- Vanilla JavaScript

---

## Notes

- The tool runs fully in the browser.
- Files are processed locally by the browser.
- Large PCD files may require significant memory.
- Rendering performance depends on browser and GPU capability.
- The waypoint planner edits waypoint poses only; it is not a global path search algorithm.

---

## License

MIT License
