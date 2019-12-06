本章节主要介绍Artinx机器人套件在Ubuntu 18.04 上的部署与简单应用。
本章节所有步骤均在`Intel NUC8I5BEH6`平台上测试通过，其他X86_64平台基本均可适用。

## 软件依赖配置

### 通用依赖

```
sudo apt install -y git vim build-essential htop wget
```

### ROS（机器人操作系统）

ROS的安装可以参考ROS Wiki的[安装教程](http://wiki.ros.org/melodic/Installation/Ubuntu)安装ROS。

```bash
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.ustc.edu.cn/ros/ubuntu/ `lsb_release -cs` main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
sudo apt update
sudo apt install ros-melodic-desktop-full
sudo rosdep init
rosdep update
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
sudo apt install python-rosinstall python-rosinstall-generator python-wstool build-essential
```

### 其它推荐（选装）

- terminator （终端分屏）

## 应用包的安装与配置

```bash

# 创建工作空间文件夹
mkdir -p ~/roborts_edu_ws/src
# 切换至src目录
cd roborts_edu_ws
# 下载RoboRTS-Edu及相关软件包源码（此处以RoboRTS-Edu搭配SLAM包和RPlidar驱动包举例）
wget -c https://raw.githubusercontent.com/Artinx-Robotics/Common/master/roborts-melodic-slam-rplidar.rosinstall
wstool init src roborts-melodic-slam-rplidar.rosinstall
wstool update -t src
#若未初始化过rosdep，请运行下行，否则跳过即可
#sudo rosdep init
#rosdep安装依赖
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
# 编译源码
cd ~/roborts_edu_ws
catkin_make -j
# 加载环境变量
source devel/setup.bash
# 若使用zsh用下行替代
# source devel/setup.zsh
```

> [!Tip]
>
> 建议把`source`写入bashrc，方便随时启动。

## 更新Udev配置

```bash
#source roborts_ws/devel/setup.bash
roscd roborts_bringup/scripts/udev
./create_ude_rules.sh
```

脚本会自动复制`roborts_bringup/scripts/udev`文件夹中的`roborts.rules`到`/etc/udev/rules.d`， 并重新加载和启动`udev`。配置成功后需要重新插拔相关的usb插口。

> [!Tip]
>
> 关于udev的配置，可以参考这篇[教程](https://www.clearpathrobotics.com/assets/guides/ros/Udev%20Rules.html)

## 运行例程

> [!Note]
>
> 例程模式适用rplidar a2作为激光雷达传感器，如替换其他传感器，请修改对应参数。

1. 启动SLAM演示

```bash
#需要Rviz
roslaunch roborts_bringup roborts_slam_with_rviz.launch

#无需Rviz
roslaunch roborts_bringup roborts_slam.launch
```


> [!Tip]
>
> 可以使用[map_server](http://wiki.ros.org/map_server#map_saver)将建好的地图(pgm和yaml)保存下来, 放入`roborts_bringup`包的map文件夹中，提供给`roborts_localization`包使用。

2. 启动已知地图的导航演示
```bash
#需要Rviz
roslaunch roborts_bringup roborts_with_rviz.launch

#无需Rviz
roslaunch roborts_bringup roborts.launch

```


