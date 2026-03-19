---
layout: post
title: "ROS2 环境配置与Turtlesim入门"
date: 2024-03-19
categories: ros2
tags: [ros2, environment, turtlesim, rqt]
reading_time: 10
---

本文介绍如何配置ROS2环境，并使用Turtlesim工具开始ROS2学习之旅。

> **参考文档**:
> - [Configuring environment](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html)
> - [Using turtlesim, ros2, and rqt](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Introducing-Turtlesim/Introducing-Turtlesim.html)

## 一、环境配置

### 1.1 背景

ROS2 依赖于通过 shell 环境组合工作空间的概念。

- **工作空间（Workspace）**：在系统上使用 ROS2 开发的位置
- **底层（Underlay）**：核心 ROS2 工作空间
- **覆盖层（Overlay）**：后续的本地工作空间

开发 ROS2 时，通常会同时激活多个工作空间。通过组合工作空间，可以更容易地针对不同版本的 ROS2 或不同的包集合进行开发。

### 1.2 加载环境配置

每次打开新终端时，需要加载 ROS2 设置文件：

```bash
source /opt/ros/jazzy/setup.bash
```

> **注意**：如果使用的是 zsh 或其他 shell，请将 `setup.bash` 替换为 `setup.zsh` 或 `setup.sh`。

### 1.3 添加到启动脚本

为避免每次打开新终端都要手动加载，可以添加到 shell 启动脚本：

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
```

### 1.4 检查环境变量

验证环境变量是否正确设置：

```bash
printenv | grep -i ROS
```

应该看到类似以下输出：

```
ROS_VERSION=2
ROS_PYTHON_VERSION=3
ROS_DISTRO=jazzy
```

### 1.5 设置 ROS_DOMAIN_ID

#### 什么是 ROS_DOMAIN_ID？

**ROS_DOMAIN_ID 就像是"频道号"，只有同一频道的 ROS2 节点才能互相通信。**

#### 生活类比

想象一个教室里有多个小组在做实验：

| 场景 | ROS2 对应 |
|-----|----------|
| 小组 A 在频道 0 讨论 | DOMAIN_ID=0 的节点互相通信 |
| 小组 B 在频道 1 讨论 | DOMAIN_ID=1 的节点互相通信 |
| 不同小组互不干扰 | 不同 DOMAIN_ID 的节点互不通信 |

#### 实际应用场景

**场景 1：多机器人系统**

```
机器人 A（DOMAIN_ID=10）
├── 导航节点
├── 感知节点
└── 控制节点
    ↓ 只与同 ID 的节点通信

机器人 B（DOMAIN_ID=20）
├── 导航节点
├── 感知节点
└── 控制节点
    ↓ 只与同 ID 的节点通信

两个机器人互不干扰！
```

**场景 2：教室实验**

10 组学生，每组设置不同的 DOMAIN_ID（1-10），避免话题冲突。

#### 默认值

ROS2 默认 DOMAIN_ID = 0，所有未设置的节点都在同一个频道。

#### 验证实验

**终端 1**（DOMAIN_ID=0）：

```bash
export ROS_DOMAIN_ID=0
ros2 run demo_nodes_cpp talker
```

**终端 2**（DOMAIN_ID=0）：

```bash
export ROS_DOMAIN_ID=0
ros2 run demo_nodes_py listener
# ✅ 能收到消息
```

**终端 3**（DOMAIN_ID=1）：

```bash
export ROS_DOMAIN_ID=1
ros2 run demo_nodes_py listener
# ❌ 收不到消息，因为频道不同
```

#### 设置方法

临时设置（当前终端有效）：

```bash
export ROS_DOMAIN_ID=<your_domain_id>
```

永久设置（添加到启动脚本）：

```bash
echo "export ROS_DOMAIN_ID=<your_domain_id>" >> ~/.bashrc
```

---

## 二、Turtlesim 入门

### 2.1 什么是 Turtlesim？

Turtlesim 是一个轻量级模拟器，用于学习 ROS2 的基本概念。它展示了 ROS2 最基本的功能，帮助你了解将来在真实机器人或机器人仿真中要做什么。

### 2.2 安装 Turtlesim

```bash
sudo apt update
sudo apt install ros-jazzy-turtlesim
```

检查是否安装成功：

```bash
ros2 pkg executables turtlesim
```

应该看到：

```
turtlesim draw_square
turtlesim mimic
turtlesim turtle_teleop_key
turtlesim turtlesim_node
```

### 2.3 启动 Turtlesim

打开一个终端，运行：

```bash
ros2 run turtlesim turtlesim_node
```

你会看到模拟器窗口出现，中间有一只随机的乌龟。

### 2.4 控制乌龟

打开另一个终端，运行：

```bash
ros2 run turtlesim turtle_teleop_key
```

现在你可以使用键盘方向键控制乌龟移动。乌龟会在移动时画出路径。

> **注意**：确保运行 `turtle_teleop_key` 的终端处于活跃状态，键盘输入才能生效。

### 2.5 查看 ROS2 元素

你可以使用 `ros2` 命令查看节点、话题、服务和动作：

```bash
ros2 node list
ros2 topic list
ros2 service list
ros2 action list
```

---

## 三、使用 rqt 工具

### 3.1 安装 rqt

```bash
sudo apt update
sudo apt install '~nros-jazzy-rqt*'
```

运行 rqt：

```bash
rqt
```

### 3.2 调用服务

首次运行 rqt 时窗口是空白的。选择菜单 **Plugins > Services > Service Caller**。

#### 生成新乌龟

1. 点击刷新按钮确保所有服务可用
2. 选择 `/spawn` 服务
3. 设置参数：
   - `name`: `turtle2`
   - `x`: `1.0`
   - `y`: `1.0`
4. 点击 **Call** 按钮

新乌龟会出现在指定位置。

#### 修改画笔颜色

使用 `/turtle1/set_pen` 服务：

- `r`: 255 (红色)
- `g`: 0
- `b`: 0
- `width`: 5

调用服务后，`turtle1` 的画笔会变成红色粗线。

### 3.3 话题重映射

要控制 `turtle2`，需要重映射话题：

```bash
ros2 run turtlesim turtle_teleop_key --ros-args --remap turtle1/cmd_vel:=turtle2/cmd_vel
```

现在这个终端控制 `turtle2`，原来的终端控制 `turtle1`。

### 3.4 关闭 Turtlesim

- 在 `turtlesim_node` 终端按 `Ctrl + C`
- 在 `turtle_teleop_key` 终端按 `q`

---

## 四、总结

通过本教程，你学会了：

1. 如何配置 ROS2 环境变量
2. 使用 Turtlesim 模拟器
3. 使用 `ros2` 命令行工具查看节点、话题、服务等
4. 使用 rqt 图形界面工具调用服务
5. 话题重映射的概念

## 下一步

继续学习 ROS2 核心概念：

- [理解节点（Nodes）](#)
- [理解话题（Topics）](#)
- [理解服务（Services）](#)

---

*持续更新中...*