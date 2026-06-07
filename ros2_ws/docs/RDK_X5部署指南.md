# RDK X5 智能小车部署指南

---

## 目录

1. [准备工作](#一准备工作)
2. [连接到小车](#二连接到小车)
3. [环境配置](#三环境配置)
4. [代码部署](#四代代码部署)
5. [测试验证](#五测试验证)
6. [比赛现场操作](#六比赛现场操作)

---

## 一、准备工作

### 1.1 硬件确认

| 设备 | 状态 | 说明 |
|------|------|------|
| RDK X5 | ✅ | 已烧录官方固件 |
| OriginCar底盘 | ✅ | 已连接 |
| 摄像头 | ✅ | USB/CSI摄像头已连接 |
| 激光雷达 | ✅ | 已连接并驱动正常 |
| 电源 | ✅ | 电量充足（建议≥80%） |
| WiFi/网线 | ✅ | 网络连接正常 |

### 1.2 获取小车IP地址

**方法一：通过串口（首次连接）**
```bash
# 使用串口工具连接（如Putty、minicom）
# 串口参数：115200 baud, 8N1
# 登录后查看IP
ifconfig  # 或 ip addr
```

**方法二：通过路由器管理页面**
- 登录路由器后台查看设备列表
- 找到设备名为 `rdk` 或 `ubuntu` 的设备

**方法三：使用mDNS（推荐）**
```bash
# 如果小车已联网，可以使用
ping rdk.local
```

**默认登录信息**
- IP: `192.168.1.xxx`（根据您的网络配置）
- 用户名: `ubuntu`
- 密码: `ubuntu`

### 1.3 本地准备

```bash
# 确保本地有完整的代码
cd y:\origin car\ros2_ws

# 压缩代码以便传输
tar -czvf competition_code.tar.gz src/ requirements.txt package.json
```

---

## 二、连接到小车

### 2.1 使用远程操控App

如果您有官方远程操控App：
1. 打开App并连接到小车
2. 找到"终端"或"SSH"功能
3. 进入命令行界面

### 2.2 使用SSH连接（推荐）

```bash
# Windows使用PowerShell或Putty
ssh ubuntu@<小车IP地址>

# 例如
ssh ubuntu@192.168.1.100

# 输入密码：ubuntu
```

### 2.3 使用VSCode远程连接（推荐）

1. 安装VSCode扩展：`Remote - SSH`
2. 按 `Ctrl+Shift+P` 打开命令面板
3. 选择 `Remote-SSH: Connect to Host...`
4. 输入：`ssh ubuntu@<小车IP>`
5. 输入密码连接

---

## 三、环境配置

### 3.1 更新系统

```bash
# 连接成功后，更新系统
sudo apt update && sudo apt upgrade -y
```

### 3.2 安装ROS2 Humble

```bash
# 检查是否已安装ROS2
ros2 --version

# 如果未安装，执行以下命令安装
sudo apt install -y ros-humble-desktop
```

### 3.3 安装依赖包

```bash
# 安装基础依赖
sudo apt install -y python3-pip python3-colcon-common-extensions

# 安装串口驱动（用于激光雷达）
sudo apt install -y python3-serial

# 安装OpenCV相关依赖
sudo apt install -y python3-opencv libopencv-dev
```

### 3.4 安装Python依赖

```bash
# 创建工作空间
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws

# 安装项目依赖
pip install ultralytics pyzbar requests numpy --user
```

---

## 四、代码部署

### 4.1 传输代码到小车

**方法一：使用scp命令**
```bash
# 在本地PowerShell中执行
scp competition_code.tar.gz ubuntu@<小车IP>:~/ros2_ws/
```

**方法二：使用Git（推荐）**
```bash
# 在小车上执行
cd ~/ros2_ws/src
git clone <您的代码仓库地址>

# 如果已有仓库，更新代码
git pull
```

**方法三：使用U盘**
```bash
# 将压缩包复制到U盘，插入小车
sudo mount /dev/sda1 /mnt/usb
cp /mnt/usb/competition_code.tar.gz ~/ros2_ws/
sudo umount /mnt/usb
```

### 4.2 解压代码

```bash
cd ~/ros2_ws
tar -xzvf competition_code.tar.gz
```

### 4.3 编译项目

```bash
cd ~/ros2_ws

# 设置ROS2环境
source /opt/ros/humble/setup.bash

# 编译所有功能包
colcon build --symlink-install

# 如果编译失败，可能需要安装缺失的依赖
# 安装colcon构建工具
sudo apt install -y python3-colcon-common-extensions
```

### 4.4 设置环境变量

```bash
# 设置工作空间环境变量
source ~/ros2_ws/install/setup.bash

# 可选：将环境变量添加到.bashrc，下次登录自动生效
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
```

---

## 五、测试验证

### 5.1 硬件测试

```bash
# 测试摄像头
ros2 run image_tools showimage

# 测试激光雷达（查看话题是否有数据）
ros2 topic echo /scan --noarr | head -20

# 测试电机（小心！确保小车在空旷地方）
ros2 topic pub /cmd_vel geometry_msgs/msg/Twist "linear:
  x: 0.2
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 0.0" -r 10
# 按Ctrl+C停止

# 测试语音播报
ros2 run voice_output voice_output_node
ros2 topic pub /voice_message std_msgs/msg/String "data: '测试语音播报'"
```

### 5.2 功能模块测试

```bash
# 测试二维码识别
ros2 launch qr_detector qr_detector.launch.py
# 在小车前放一个二维码，查看识别结果
ros2 topic echo /qr_task

# 测试人形检测
ros2 launch human_detector human_detector.launch.py
ros2 topic echo /human_description

# 测试导航
ros2 launch navigation navigation.launch.py
ros2 topic echo /cmd_vel
```

### 5.3 完整测试

```bash
# 启动完整比赛流程（在空旷场地测试！）
ros2 launch task_manager competition.launch.py

# 查看任务状态
ros2 topic echo /task_status

# 查看任务进度
ros2 topic echo /task_progress
```

---

## 六、比赛现场操作

### 6.1 赛前准备清单

```
1. 硬件检查
   ├─ 电源电量（≥80%）
   ├─ 传感器连接
   ├─ 网络连接（WiFi或热点）
   └─ 场地熟悉

2. 软件准备
   ├─ 连接SSH
   ├─ 启动终端
   └─ 测试传感器

3. 参数调整（根据实际情况）
   ├─ 修改navigation_params.yaml
   └─ 调整速度参数
```

### 6.2 启动比赛

```bash
# 方式一：使用launch文件（推荐）
cd ~/ros2_ws
source install/setup.bash
ros2 launch task_manager competition.launch.py

# 方式二：创建启动脚本
cat > start_competition.sh << EOF
#!/bin/bash
cd ~/ros2_ws
source /opt/ros/humble/setup.bash
source install/setup.bash
ros2 launch task_manager competition.launch.py
EOF

chmod +x start_competition.sh
./start_competition.sh
```

### 6.3 比赛中监控

```bash
# 打开多个终端窗口（使用tmux或多个SSH连接）

# 终端1：任务状态
ros2 topic echo /task_status

# 终端2：速度指令
ros2 topic echo /cmd_vel

# 终端3：二维码任务
ros2 topic echo /qr_task

# 终端4：人形检测结果
ros2 topic echo /human_description
```

### 6.4 紧急停止

```bash
# 方法一：发布零速度指令
ros2 topic pub /cmd_vel geometry_msgs/msg/Twist "linear:
  x: 0.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 0.0" -r 10

# 方法二：直接终止节点
Ctrl+C  # 在运行launch的终端中

# 方法三：断开电源（最后手段）
```

### 6.5 赛后总结

```bash
# 保存比赛日志
ros2 bag record /qr_task /human_description /task_status /cmd_vel /scan -O competition_result

# 导出日志
ros2 bag info competition_result
```

---

## 常见问题解决

### 问题1：SSH连接失败
```bash
# 检查网络连接
ping <小车IP>

# 检查防火墙
sudo ufw status

# 重启SSH服务
sudo systemctl restart sshd
```

### 问题2：摄像头无图像
```bash
# 检查设备
ls /dev/video*

# 检查权限
ls -la /dev/video0

# 添加用户到video组
sudo usermod -aG video ubuntu
```

### 问题3：激光雷达无数据
```bash
# 检查串口
ls /dev/ttyUSB*

# 设置权限
sudo chmod 666 /dev/ttyUSB0

# 检查驱动
ros2 launch rplidar_ros rplidar.launch.py  # 根据雷达型号
```

### 问题4：编译失败
```bash
# 清理构建缓存
rm -rf build/ install/ log/

# 重新编译
colcon build --symlink-install --packages-select qr_detector
```

### 问题5：节点启动失败
```bash
# 查看错误信息
ros2 run qr_detector qr_detector_node

# 检查依赖
pip list | grep pyzbar

# 安装缺失依赖
pip install <缺失的包>
```

---

## 附录：常用命令速查

| 命令 | 说明 |
|------|------|
| `ros2 launch task_manager competition.launch.py` | 启动完整比赛 |
| `ros2 topic echo <话题名>` | 查看话题数据 |
| `ros2 node list` | 查看运行中的节点 |
| `ros2 param set <节点> <参数> <值>` | 动态设置参数 |
| `colcon build` | 编译工作空间 |
| `source install/setup.bash` | 设置环境变量 |
| `Ctrl+C` | 终止当前进程 |
| `ros2 bag record` | 录制数据 |

---

**文档版本**: v1.0  
**创建日期**: 2025年  
**队伍名称**: [填写队伍名称]  
**学校名称**: [填写学校名称]
