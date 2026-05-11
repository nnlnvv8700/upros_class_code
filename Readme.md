# upros_class_code

小车基础 ROS 工程，包含底盘 bringup、导航、建图、传感器、机械臂、视觉、语音等基础功能包。

本仓库是 ROS1 catkin 工作空间，是 `clean_robot_code` 的基础依赖。运行比赛任务前，应先编译本工程。

## 目录结构

- `src/upros_bringup`：不同车型的底盘、雷达、相机等 bringup launch。
- `src/w2u_navigation`：W2 系列导航配置和 launch，`clean_robot_code` 的 W2A 桌面清洁流程会使用它。
- `src/upros_navigation`：通用导航、建图、路径点等功能。
- `src/upros_message`：自定义 ROS 消息，其他包编译前需要先生成。
- `src/upros_depth_vision`：深度相机、颜色、AprilTag、YOLO 等视觉相关功能。
- `src/upros_arm`、`src/zoo_arm`：机械臂控制相关功能。
- `test_shell/`：按车型和功能整理的测试启动脚本。

## 运行环境

- Ubuntu + ROS Noetic。
- 已安装 catkin 编译环境。
- 小车硬件连接正常。
- 串口、雷达、相机等设备权限已经配置好。

## 部署到小车

建议放在用户家目录：

```bash
cd ~
git clone https://gitee.com/song_gang/upros_class_code.git
cd upros_class_code
```

如果使用自己的镜像仓库，把 clone 地址替换成实际地址。

## 安装依赖

```bash
cd ~/upros_class_code
rosdepc install --from-paths src --ignore-src --rosdistro=noetic -y
```

如果系统没有 `rosdepc`，先安装并初始化：

```bash
sudo apt install python3-rosdep
sudo rosdep init
rosdep update
```

部分国内环境使用 `rosdepc`，按小车当前系统配置选择 `rosdep` 或 `rosdepc`。

## 编译

先单独编译消息包，再编译全部工程：

```bash
cd ~/upros_class_code
catkin_make --pkg upros_message
catkin_make
```

编译完成后 source 环境：

```bash
source /opt/ros/noetic/setup.bash
source ~/upros_class_code/devel/setup.bash
```

可以加入 `~/.bashrc`，方便新终端自动加载：

```bash
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
echo "source ~/upros_class_code/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

## W2A 常用检查

确认关键包可被 ROS 找到：

```bash
rospack find upros_bringup
rospack find w2u_navigation
rospack find upros_message
```

单独启动 W2A 底盘和传感器：

```bash
roslaunch upros_bringup bringup_w2a.launch
```

单独启动 W2 系列导航：

```bash
roslaunch w2u_navigation navigation.launch
```

如果要配合 `clean_robot_code` 的桌面清洁流程，先确认本工程编译和 source 正常，再编译并运行 `clean_robot_code`。

## 测试脚本

`test_shell/w2a/` 下有 W2A 相关功能测试脚本，例如：

- `2-odom.sh`：里程计相关测试。
- `3-arm_grab.sh`：机械臂抓取相关测试。
- `5-mapping.sh`：建图相关测试。
- `7-navigation.sh`：导航相关测试。
- `15-clean_desktop.sh`：桌面清洁流程旧版分窗口启动脚本。

这些脚本多依赖 `gnome-terminal` 多标签页启动，比赛运行建议优先使用 `clean_robot_code/run_clean_desktop_w2a.sh`。

## 常见问题

### 编译时报找不到消息类型

先编译 `upros_message`：

```bash
cd ~/upros_class_code
catkin_make --pkg upros_message
catkin_make
```

### 找不到 ROS 包

确认已经 source：

```bash
source /opt/ros/noetic/setup.bash
source ~/upros_class_code/devel/setup.bash
```

再用 `rospack find <包名>` 检查。

### 设备权限异常

检查小车上的 udev 规则和串口权限。仓库中已有部分规则文件，例如：

- `src/upros_bringup/rules/`
- `test_shell/update_udev_*.sh`

根据实际车型安装对应规则后，重新插拔设备或重启 udev。
