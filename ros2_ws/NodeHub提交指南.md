# NodeHub 竞赛方案提交指南

## 一、项目准备

### 1. 项目结构调整

确保项目结构符合NodeHub要求：

```
origin-car-medical/          # 项目根目录（重命名）
├── src/                     # 源代码
│   ├── qr_detector/
│   ├── human_detector/
│   ├── navigation/
│   ├── voice_output/
│   └── task_manager/
├── docs/                    # 文档
│   ├── 技术文档.md
│   └── README.md
├── config/                  # 配置文件
│   └── navigation_params.yaml
├── launch/                  # 启动文件
│   └── competition.launch.py
├── requirements.txt         # 依赖
└── package.json             # NodeHub项目描述（新增）
```

### 2. 创建NodeHub项目描述文件

新建 `package.json` 或 `nodehub.json`：

```json
{
  "name": "origin-car-medical-competition",
  "version": "1.0.0",
  "description": "第二十届全国大学生智能汽车竞赛-地瓜机器人智慧医疗挑战赛参赛方案",
  "main": "launch/competition.launch.py",
  "author": "[队伍名称]",
  "organization": "[学校名称]",
  "license": "Apache-2.0",
  "platforms": ["RDK X3", "RDK X5", "RDK S100"],
  "categories": ["比赛专区", "综合应用", "人机交互"],
  "keywords": ["智慧医疗", "OriginCar", "智能车", "竞赛", "大模型"],
  "dependencies": {
    "rclpy": "*",
    "ultralytics": ">=8.0.0",
    "opencv-python": ">=4.8.0"
  },
  "hardware": {
    "robot": "OriginCar",
    "sensors": ["单目摄像头", "深度摄像头", "激光雷达", "IMU"],
    "controller": "RDK X5"
  }
}
```

## 二、NodeHub 上传步骤

### 1. 注册登录

1. 访问 https://developer.d-robotics.cc/nodehub
2. 使用地瓜机器人社区账号登录
3. 进入个人中心 -> 我的项目

### 2. 创建新项目

1. 点击 "创建项目" 或 "上传项目"
2. 填写项目信息：
   - 项目名称：`[学校名称]-[队伍名称]-智慧医疗竞赛`
   - 项目描述：参考技术文档的摘要
   - 分类：选择 "比赛专区" + "综合应用"
   - 运行平台：选择您使用的RDK型号

### 3. 上传代码

**方式一：Git仓库（推荐）**
1. 将项目推送到 GitHub/Gitee
2. 在NodeHub中选择 "从Git仓库导入"
3. 填入仓库地址

**方式二：文件上传**
1. 将整个项目打包为ZIP
2. 在NodeHub中选择 "上传压缩包"
3. 上传项目文件

### 4. 完善项目信息

1. 上传项目封面图（OriginCar在场地运行的照片）
2. 填写项目介绍（复制技术文档内容）
3. 添加使用说明
4. 设置可见性（公开/仅参赛可见）

## 三、项目内容检查清单

- [ ] 完整的ROS2工作空间
- [ ] 所有代码都有中文注释
- [ ] 技术文档（Word+PDF版本）
- [ ] README说明文档
- [ ] 项目演示视频（可选但推荐）
- [ ] requirements.txt依赖文件
- [ ] package.json项目描述
- [ ] 各功能包的launch启动文件

## 四、赛前备案和赛后提交

### 赛前备案（必须）

- 提交时间：比赛开始前
- 提交内容：项目初版 + 队伍信息
- 目的：用于赛事组织和技术支持

### 赛后终版（推荐）

- 提交时间：比赛结束后3个工作日内
- 提交内容：最终参赛代码 + 技术总结
- 可包含：比赛过程中的问题和解决方法

## 五、技术文档内容要求

技术文档应包含（参考我们已有的`技术文档.md`）：

1. **项目概述** - 背景、目标、功能
2. **系统架构** - 硬件架构、软件架构
3. **核心算法** - 二维码识别、人形检测、路径规划等
4. **创新点** - 技术创新和功能创新
5. **部署方案** - 硬件安装、软件部署、参数配置
6. **任务实现** - 各子任务的实现情况
7. **性能评估** - 测试数据和指标
8. **结论展望** - 总结和改进方向

## 六、NodeHub项目信息填写模板

### 项目标题
```
[学校名称]-[队伍名称]-智慧医疗竞赛方案
```

### 项目简介
```
本项目是第二十届全国大学生智能汽车竞赛-地瓜机器人智慧医疗挑战赛的参赛方案。
项目实现了二维码识别、人形检测与大模型描述、路径规划与导航、激光避障、语音播报等功能。
```

### 使用说明
```
1. 环境准备
   - 安装Ubuntu 22.04 + ROS2 Humble
   - 安装Python依赖：pip install -r requirements.txt

2. 编译项目
   cd /path/to/project
   colcon build --symlink-install
   source install/setup.bash

3. 运行
   ros2 launch task_manager competition.launch.py
```

### 标签
```
智慧医疗, OriginCar, 竞赛, RDK, 机器人, 大模型, YOLO
```

## 七、注意事项

1. **命名规范** - 项目名称和文件命名统一使用英文或拼音
2. **文档完整性** - 确保技术文档内容完整，包含所有要求的部分
3. **代码质量** - 保持代码注释清晰，结构合理
4. **硬件配置** - 在文档中明确说明使用的RDK型号和传感器配置
5. **版本控制** - 建议使用Git管理代码版本
6. **知识产权** - 保护原创代码，NodeHub提供相应的权限管理

祝在NodeHub上提交顺利！🎯
