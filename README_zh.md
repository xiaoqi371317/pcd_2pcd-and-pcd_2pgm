# ROS 2 Nav Studio：PCD 地图编辑器与路径规划工具

一个轻量级、浏览器端运行的 ROS 2 地图预处理工具，用于原始 PCD 点云查看、点云起点重设、刚体变换导出、二维占据栅格地图编辑以及航点路径规划。

本项目是一个单页面 HTML 应用，主要基于 Three.js 和 HTML5 Canvas 实现。它适合用于机器人导航建图流程中，对 LiDAR 生成的 `.pcd` 点云进行可视化、坐标系调整、地图原点重设、二维地图生成、地图修补以及航点导出。

## 项目简介

ROS 2 Nav Studio 提供一套完整的浏览器端地图处理流程：

- 查看原始 `.pcd` 点云
- 使用 Tx / Ty / Tz / Roll / Pitch / Yaw 调整激光雷达初始位姿
- 在三维点云中交互式拖动、旋转目标起点位姿
- 自动生成 PCD 重设起点所需的变换参数
- 导出经过刚体变换后的新 `.pcd` 文件
- 从 PCD 点云生成 ROS 可用的二维占据栅格地图
- 编辑已有 `PGM + YAML` 地图
- 创建带方向角的路径航点并导出为 ROS 参数格式

整个工具完全在浏览器中运行，不需要后端服务器。

## 功能特性

### 1. 原始 PCD 点云查看器

- 支持加载 `.pcd` 点云文件
- 使用 Three.js 进行三维点云渲染
- 支持鼠标旋转、平移、缩放视角
- 可调节点云显示点大小
- 可视化当前 LiDAR 初始位姿
- 使用红、绿、蓝三色坐标轴显示 Roll / Pitch / Yaw 方向
- 坐标轴末端显示 `Roll`、`Pitch`、`Yaw` 标签
- 支持 Grid 方格基座尺寸、网格大小、中心线颜色、普通网格颜色调整

### 2. 目标起点位姿拾取

为了方便 PCD 点云重定位和地图原点重设，工具提供了交互式目标起点位姿拾取功能。

用户可以在原始点云中创建第二套红绿蓝坐标轴，用作新的目标起点位姿：

- 支持创建 / 显示目标起点位姿
- 支持平移拖动目标位姿
- 支持旋转调整目标姿态
- 支持从当前初始位姿复制
- 支持将目标位姿放置到点云中心
- 支持清除目标起点位姿
- 支持复制目标位姿参数
- 目标位姿参数会保存到浏览器本地存储中，刷新后可恢复

确认目标位姿后，工具会自动生成用于 PCD 重设起点的参数：

- `Start X`
- `Start Y`
- `Start Z`
- `Roll`
- `Pitch`
- `Yaw`
- `Out X / Y / Z`

并可一键写入 PCD 变换导出面板。

### 3. PCD 刚体变换与导出

工具支持对 PCD 点云进行刚体变换，并导出新的 `.pcd` 文件。

支持两种变换模式：

#### 重设起点模式

用于将旧点云地图中的任意位姿设为新的建图起点。

变换公式：

```text
P_out = R_start^T · (P_in - Start) + Out
```

该模式适合：

- 重设 PCD 地图原点
- 模拟机器人从另一个位置开始建图
- 为点云重定位准备先验地图
- 将旧地图坐标系转换为新的局部坐标系

#### 直接刚体变换模式

用于围绕指定中心旋转整张点云，并额外平移。

变换公式：

```text
P_out = R · (P_in - Center) + Center + T
```

该模式适合：

- 整体旋转点云
- 整体平移点云
- 手动修正点云坐标
- 对齐不同坐标系下的 PCD 文件

### 4. PCD 到占据栅格地图生成

工具可以从 PCD 点云生成二维占据栅格地图。

可配置参数包括：

- 地图分辨率 `Resolution`
- 高度过滤下限 `Min Z`
- 高度过滤上限 `Max Z`
- LiDAR 位姿修正参数：
  - `Tx`
  - `Ty`
  - `Tz`
  - `Roll`
  - `Pitch`
  - `Yaw`

生成后会得到可编辑的二维地图画布。

### 5. 地图编辑器

支持对生成的二维占据栅格地图或已有地图进行编辑。

功能包括：

- 加载已有 `YAML + PGM` 地图
- 显示地图坐标
- 平移 / 缩放画布
- 使用画笔补墙
- 使用橡皮擦擦除障碍物
- 调整笔刷大小
- 修改地图分辨率
- 修改地图 Origin
- 支持 Y 轴翻转
- 导出 ROS 可用的 `PGM + YAML`

### 6. 路径规划器 / 航点编辑器

工具内置简单的航点路径规划界面。

功能包括：

- 鼠标左键点击确定航点位置
- 拖拽确定航点朝向
- 自动计算航点 yaw
- 显示航点编号和方向箭头
- 支持撤销上一步
- 支持清空所有航点
- 导出 ROS 参数格式的航点列表

导出格式示例：

```yaml
patrol_sender:
  ros__parameters:
    num_waypoints: 3
    waypoint_0: [x, y, yaw]
    waypoint_1: [x, y, yaw]
    waypoint_2: [x, y, yaw]
```

## 快速开始

### 1. 克隆项目

```bash
git clone <your-repository-url>
cd <your-repository-name>
```

### 2. 打开 HTML 文件

直接用浏览器打开主 HTML 文件即可：

```bash
ros2_nav_studio_pcd_transform_export_target_pose_picker.html
```

也可以使用简单的本地 HTTP 服务：

```bash
python3 -m http.server 8000
```

然后在浏览器中访问：

```text
http://localhost:8000
```

## 推荐使用流程

### PCD 点云重设起点

1. 打开网页工具
2. 在“地图编辑器”中选择 `.pcd` 文件
3. 切换到“原始点云”页面
4. 点击“加载 / 刷新原始点云”
5. 点击“创建 / 显示目标起点位姿”
6. 使用平移 / 旋转模式调整目标起点
7. 点击“应用到重设起点参数并切回导出面板”
8. 在 PCD 变换导出区域确认参数
9. 点击“变换并导出新的 PCD”

### 从 PCD 生成二维地图

1. 选择 `.pcd` 文件
2. 设置地图分辨率
3. 设置 `Min Z / Max Z` 高度过滤范围
4. 设置 LiDAR 初始位姿修正参数
5. 点击“生成地图”
6. 使用地图编辑器修补或擦除障碍物
7. 导出 `PGM + YAML`

### 编辑已有地图

1. 选择 `.yaml` 文件
2. 选择对应的 `.pgm` / `.png` / `.jpg` 地图文件
3. 点击“加载地图”
4. 使用补墙 / 擦除工具编辑地图
5. 校准 Resolution 和 Origin
6. 导出新的地图文件

### 创建航点路径

1. 切换到“路径规划器”
2. 左键点击确定航点位置
3. 按住拖动确定航点方向
4. 重复添加多个航点
5. 复制导出的 ROS 参数

## 坐标与变换说明

### RPY 约定

工具使用常见 ROS 欧拉角顺序：

```text
R = Rz(yaw) * Ry(pitch) * Rx(roll)
```

其中：

- Roll：绕 X 轴旋转
- Pitch：绕 Y 轴旋转
- Yaw：绕 Z 轴旋转

### 坐标轴颜色

三维查看器中：

- 红色：X 轴 / Roll 方向
- 绿色：Y 轴 / Pitch 方向
- 蓝色：Z 轴 / Yaw 方向

### 重设起点模式说明

当用户在旧 PCD 地图中选择一个新的起点位姿时，工具会将该位姿作为新坐标系原点。

公式为：

```text
P_out = R_start^T · (P_in - Start) + Out
```

通常情况下，`Out X / Y / Z` 保持为 0。

## 支持的输入输出

### 输入

- `.pcd`
- `.yaml`
- `.pgm`
- `.png`
- `.jpg`

### 输出

- 变换后的 `.pcd`
- ROS 占据栅格地图 `.pgm`
- ROS 地图描述文件 `.yaml`
- ROS 航点参数文本

## PCD 支持情况

当前支持：

- `DATA ascii`
- `DATA binary`

暂不支持：

- `DATA binary_compressed`

如果你的 PCD 是 `binary_compressed` 格式，可以先使用 PCL 工具转换为 ascii 或 binary 格式。

## 截图

可以在仓库中加入以下截图：

```markdown
![Raw PCD Viewer](docs/images/raw_pcd_viewer.png)
![Target Pose Picker](docs/images/target_pose_picker.png)
![Waypoint Planner](docs/images/waypoint_planner.png)
![Map Editor](docs/images/map_editor.png)
```

建议将截图放到：

```text
docs/images/
```

## 项目结构建议

```text
.
├── README.md
├── README_zh.md
├── ros2_nav_studio_pcd_transform_export_target_pose_picker.html
└── docs
    └── images
        ├── raw_pcd_viewer.png
        ├── target_pose_picker.png
        ├── waypoint_planner.png
        └── map_editor.png
```

## 使用场景

本工具适用于：

- ROS 2 Navigation 地图预处理
- Nav2 地图准备
- LiDAR 点云重定位
- PCD 地图起点重设
- 多次建图结果坐标统一
- 点云地图坐标修正
- 二维占据栅格地图生成
- 手动地图修补
- 巡逻路径航点编辑

## 注意事项

- 本工具在浏览器中运行，处理大型 PCD 文件时会占用较多内存
- PCD 点云过大时，加载和渲染速度取决于浏览器和显卡性能
- 变换导出会直接在浏览器中完成，不会上传文件
- 使用生成的地图前，请确认 Resolution 和 Origin 是否正确
- 路径规划器只负责航点编辑，不包含全局路径搜索算法
- PCD 重设起点参数需要结合实际机器人坐标系进行验证

## 依赖

工具通过浏览器加载以下前端库：

- Three.js
- PCDLoader
- OrbitControls

如果部署在离线环境中，建议将相关 JavaScript 依赖下载到本地并修改 import 路径。

## License

MIT License
