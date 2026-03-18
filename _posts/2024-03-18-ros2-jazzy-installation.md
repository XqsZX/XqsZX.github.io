---
layout: post
title: "ROS2 Jazzy 安装教程"
date: 2024-03-18
categories: ros2
tags: [ros2, installation, ubuntu]
---

本文记录在Ubuntu 24.04上安装ROS2 Jazzy的完整步骤。

## 系统环境

- Ubuntu 24.04 LTS (Noble Numbat)
- ROS2 Jazzy Jalisco

## 安装步骤

### 1. 设置语言环境

```bash
sudo apt update && sudo apt install -y locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
```

### 2. 添加ROS2软件源

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository universe
sudo apt update && sudo apt install -y curl
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

### 3. 安装ROS2

```bash
sudo apt update
sudo apt install -y ros-jazzy-desktop
```

> **说明**: `ros-jazzy-desktop` 包含完整的桌面环境，包括RViz等可视化工具。如果只需要基础功能，可以安装 `ros-jazzy-ros-base`。

### 4. 安装开发工具

```bash
sudo apt install -y python3-pip
pip3 install --user argcomplete colcon-common-extensions
```

### 5. 配置环境变量

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 6. 验证安装

```bash
ros2 topic list
```

如果输出类似以下内容，说明安装成功：

```
/parameter_events
/rosout
```

## 常见问题

### 网络问题

如果下载速度慢，可以尝试使用国内镜像源。编辑 `/etc/apt/sources.list.d/ros2.list`，将 `packages.ros.org` 替换为国内镜像地址。

### 环境变量未生效

每次打开新终端需要执行 `source ~/.bashrc`，或者重启终端。

## 下一步

安装完成后，可以开始学习ROS2基础概念：

- [节点（Nodes）](#)
- [话题（Topics）](#)
- [服务（Services）](#)

---

*持续更新中...*