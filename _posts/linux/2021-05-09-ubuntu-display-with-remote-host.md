---
title: 怎样在没有 HDMI 的 ubuntu 系统上运行带 UI 的应用？
tags: [xserver, DISPLAY]
category: linux
image:
  path: /assets/img/headers/xserver.webp
---

有时候我们的开发板上没有 HDMI 接口，但是我们又需要在 ubuntu 系统上运行带 UI 的应用，这时候我们就可以使用 Xserver 来实现远程显示。

## 准备工作

首先，我们需要在自己的电脑上安装一个mobaxterm, 应为其内置 Xserver， 当然，你也可以安装其他软件，比如 Xming 等等。
然后配置我们自己的电脑和开发板处于同一个局域网内，并确保开发板的 IP 地址是可访问的。这里假设我们电脑的 IP 是 182.168.1.222， 开发板的 IP 是 192.168.1.100。

## 配置开发板
使用 vim 打开 .bashrc（`vim ~/.bashrc`），在末尾行添加以下内容：

```bash
export DISPLAY=192.168.1.100:0.0
```
保存并退出，然后重启开发板。

## 使用

打开 mobaxterm，使用 ssh 连接到开发板后，在连接上的终端里运行你的应用，此时你的电脑屏幕上就会显示开发板的应用的 UI 了。

## 注意

如果运行你的应用时，出现报错如下： This Application failed to start because no Qt platform plugin could be init......

那么你需要安装 qt5-default 包：

```bash
sudo apt-get install qt5-default
```