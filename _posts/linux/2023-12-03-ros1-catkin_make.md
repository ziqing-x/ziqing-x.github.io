---
title: 如何使用 catkin 构建 ROS1 的多个功能包
tags: [ROS, noetic, catkin, catkin_make]
category: linux
image:
  path: /assets/img/headers/catkin.webp
---

Catkin 是一个用于构建 ROS 软件包的工具。它是基于 CMake 构建系统的扩展，旨在简化 ROS 软件包的构建和管理。使用Catkin，开发人员可以更轻松地构建、测试和部署ROS软件包。Catkin 提供了一种结构化的方式来组织 ROS 软件包，并支持依赖管理、构建配置和自定义构建类型等功能。

## 使用

### 1、创建工作空间

```bash
mkdir catkin_ws # 工作空间的名字不固定，自行命名
mkdir catkin_ws/src # src 目录下用来存放一个或者多个功能包
```
此时的空间结构如下：
```bash
catkin_ws
└── src
```

### 2、初始化工作空间

把自己的功能包放到 catkin 工作空间里

```bash
cp my_pkg1 catkin_ws/src -r
cp my_pkg2 catkin_ws/src -r
cp my_pkg3 catkin_ws/src -r
```

初始化 catkin 工作空间

```bash
cd catkin_ws/src
catkin_init_workspace
```

此时的空间结构如下：
```bash
catkin_ws
└── src
    ├── CMakeLists.txt
    ├── my_pkg1
    ├── my_pkg2
    └── my_pkg3
```

## 编译功能包

``` bash
cd catkin_ws
catkin_make
```
此时的空间结构如下：
```bash
catkin_ws
├── build
├── devel
└── src
    ├── CMakeLists.txt
    ├── my_pkg1
    ├── my_pkg2
    └── my_pkg3
```

## 补充

每次启动功能包都需要 source catkin_ws/devel/setup.bash
所以建议写到 bashrc 里

```bash
vim ~/.bashrc
# 在最底下添加这行
source ~/catkin_ws/devel/setup.bash # 替换成自己的路径
```

## 温馨提示

如果你有多个 catkin 的工作空间时, 在~/.bashrc 里执行多个 setup.bash 脚本时，如下

```bash
source ~/catkin_ws1/devel/setup.bash
source ~/catkin_ws2/devel/setup.bash
```
此时 ~/catkin_ws2/devel/setup.bash 里有的环境变量可能会覆盖 ~/catkin_ws1/devel/setup.bash 里的一些环境变量，所以会找不到 catkin_ws1 里的功能包，所以建议在一个 catkin 工作空间里管理多个功能包。