---
title: "ROS"
date: 2025-10-17
categories: 
  - "ros"
---

## **ROS****安装**

根据Ubuntu系统来选择对应的ros版本，对应关系可参考【1、ROS简介】-【1.5、发行版本】进行选

择，本节课程以在**Ubuntu20.04**上安装**ROS-Noetic**为例，说明下如何安装ros。

## **设置****ros****源**

终端输入

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

## **设置密钥**

终端输入

```
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

## **更新源**

终端输入

sudo apt update

## **安装****ROS-Noetic**

这里安装的是基础桌面版，终端输入

```
sudo apt install ros-noetic-desktop -y
```

## **设置****ROS****环境**

把ROS的路径加入到环境变量中，方便以后打开终端的时候，可以找打ROS的运行环境，终端输入

echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc

然后重新打开终端或者source一次刷新环境变量，终端输入，

```
source ~/.bashrc
```

## **验证**

终端输入

```
roscore
```

出现以下画面则表示可以安装成功，

![](/wp-content/uploads/2025/10/图片.png)

## ROS2安装

```
# FishROS 官方一键安装命令（自动适配系统，推荐）
wget http://fishros.com/install -O fishros && . fishros

```

![](/wp-content/uploads/2025/10/图片-1.png)

```
cd /ai/ai-pcb-det
catkin_make
```

```
启动ros
roscore 
```

```
配置环境变量
source ~/ai/ai-pcb-det/devel/setup.bash
运行单个文件
rosrun 
运行launch多文件启动
roslaunch 
```
