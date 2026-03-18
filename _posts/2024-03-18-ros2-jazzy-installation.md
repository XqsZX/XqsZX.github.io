---
layout: post
title: "ROS2 Jazzy 安装教程"
date: 2024-03-18
categories: ros2
tags: [ros2, installation, ubuntu]
reading_time: 5
---

本文记录在Ubuntu 24.04上安装ROS2 Jazzy的完整步骤。

> **参考文档**: [ROS 2 Documentation: Jazzy - Ubuntu (deb packages)](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html)

## 系统环境

- Ubuntu 24.04 LTS (Noble Numbat)
- ROS2 Jazzy Jalisco

## 安装步骤

### 1. 设置语言环境

确保系统使用支持UTF-8的语言环境：

```bash
locale  # 检查当前语言设置

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # 验证设置
```

### 2. 启用所需软件源

首先确保启用了Ubuntu Universe软件源：

```bash
sudo apt install software-properties-common
sudo add-apt-repository universe
```

### 3. 添加ROS 2 apt软件源

安装 `ros2-apt-source` 包来配置ROS 2软件源：

```bash
sudo apt update && sudo apt install curl -y
export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F'"' '{print $4}')
curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo ${UBUNTU_CODENAME:-${VERSION_CODENAME}})_all.deb"
sudo dpkg -i /tmp/ros2-apt-source.deb
```

### 4. 安装开发工具（可选）

如果需要编译ROS包或进行开发：

```bash
sudo apt update && sudo apt install ros-dev-tools
```

### 5. 安装ROS 2

更新apt缓存：

```bash
sudo apt update
sudo apt upgrade
```

**桌面版安装（推荐）**：包含ROS、RViz、示例和教程。

```bash
sudo apt install ros-jazzy-desktop
```

**基础版安装**：仅包含通信库、消息包和命令行工具，无GUI工具。

```bash
sudo apt install ros-jazzy-ros-base
```

### 6. 配置环境变量

将以下内容添加到 `~/.bashrc`：

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

> **注意**: 如果使用的是zsh或其他shell，请将 `setup.bash` 替换为对应的文件：`setup.zsh` 或 `setup.sh`。

### 7. 验证安装

在一个终端中运行C++ talker：

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_cpp talker
```

在另一个终端中运行Python listener：

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_py listener
```

如果看到talker发布消息，listener接收到消息，说明C++和Python API都工作正常！

## 常见问题

### 网络问题

如果下载速度慢，可以尝试使用国内镜像源。

### 环境变量未生效

每次打开新终端需要执行 `source ~/.bashrc`，或者重启终端。

### 卸载ROS 2

如需卸载ROS 2：

```bash
sudo apt remove '~nros-jazzy-*' && sudo apt autoremove
```

移除软件源：

```bash
sudo apt remove ros2-apt-source
sudo apt update
sudo apt autoremove
sudo apt upgrade
```

## 下一步

安装完成后，可以开始学习ROS2基础概念：

- [节点（Nodes）](#)
- [话题（Topics）](#)
- [服务（Services）](#)

---

*持续更新中...*