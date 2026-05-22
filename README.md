# ROS2 Nav Studio — PCD Map Editor & Relocalization Toolkit

A lightweight browser-based toolkit for ROS2 navigation map editing, waypoint planning, and LiDAR point cloud relocalization preprocessing.

This project provides an interactive workflow for:

* Editing occupancy grid maps (`PGM + YAML`)
* Visualizing raw `.pcd` LiDAR point clouds in 3D
* Re-defining map origins / relocalization start poses
* Applying rigid body transforms to PCD maps
* Exporting transformed PCD files directly in-browser
* Planning and exporting waypoint paths for ROS2 navigation

Built with pure HTML + JavaScript + Three.js.
No backend, no installation, no ROS dependency required for visualization and preprocessing.

---

## Features

### ☁️ Raw PCD Viewer

* Native `.pcd` point cloud visualization
* Interactive orbit camera
* Adjustable point size
* Configurable 3D grid size and colors
* Roll / Pitch / Yaw axis labels
* LiDAR pose visualization

### 🎯 Interactive Relocalization Pose Editor

* Add a target start pose directly inside the point cloud
* Drag and rotate a 3D transform gizmo
* Real-time pose parameter updates
* Automatically generates:

  * Start XYZ
  * Roll / Pitch / Yaw
* One-click synchronization to PCD export transform parameters

Perfect for:

* LiDAR relocalization
* Re-mapping map origins
* Multi-session map alignment
* Changing SLAM start positions
* Dataset coordinate normalization

### 🔄 PCD Rigid Transform Export

Supports:

* ASCII PCD
* Binary PCD

Transform modes:

* Reset Start Pose
* Direct Rigid Transform

Functions:

* Rotation
* Translation
* Coordinate frame reset
* VIEWPOINT rewriting

Export transformed `.pcd` files directly from the browser.

### 🗺️ Occupancy Grid Map Editor

* Load existing `PGM + YAML`
* Generate occupancy maps from PCD
* Draw / erase obstacles
* Adjust map resolution and origin
* Export ROS-compatible maps

### 🚩 Waypoint Planner

* Click-to-create waypoints
* Direction-aware waypoint generation
* Export ROS2 navigation pose lists

---

## Typical Workflow

1. Load a raw `.pcd`
2. Open the 3D viewer
3. Drag the target relocalization pose
4. Apply pose to transform parameters
5. Export transformed PCD
6. Generate occupancy map
7. Edit map
8. Export ROS2 navigation assets

---

## Use Cases

* ROS2 Navigation
* Nav2 preprocessing
* LiDAR relocalization
* SLAM dataset preparation
* Robotics competition mapping
* Multi-session map alignment
* Tunnel / cave / narrow-passage navigation preprocessing

---

## Tech Stack

* Three.js
* PCDLoader
* OrbitControls
* Vanilla JavaScript
* HTML5 Canvas

---

## Screenshot Features

* Interactive 3D point cloud viewer
* Drag-based relocalization origin editing
* ROS-style Roll/Pitch/Yaw visualization
* Occupancy grid editor
* Waypoint planning interface

---

## Notes

* Runs fully in-browser
* No server required
* Large PCD files may require substantial RAM
* `binary_compressed` PCD is currently not supported

---

## Future Plans

* ICP alignment
* Multi-PCD registration
* Interactive cropping
* Octomap export
* ROS2 bag integration
* TF tree preview
* MINCO / spline path smoothing
* NDT relocalization helper tools

---

## License

MIT License
