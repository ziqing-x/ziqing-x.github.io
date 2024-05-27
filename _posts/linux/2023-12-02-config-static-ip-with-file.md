---
title: 配置嵌入式 linux 静态 ip
tags: [静态ip]
category: linux
image:
  path: /assets/img/headers/static-ip.webp
---

在嵌入式 Linux 系统中，经常需要将网络接口配置为静态 IP 地址，以确保设备在网络中具有固定的 IP 地址，便于管理和访问。本文将介绍如何通过编辑 /etc/network/interfaces 文件来配置静态 IP 地址。 
 
#### 了解interfaces文件 
 
 /etc/network/interfaces 文件是许多基于 Debian 的 Linux 发行版（包括 Ubuntu ）用来配置网络接口的传统方法。这个文件允许用户定义网络接口的行为，包括它们是动态获取 IP 地址还是使用静态 IP 地址。 
 
#### 步骤1：确认网络接口名称 
 
在开始配置之前，你需要确定要配置的网络接口名称。可以通过执行以下命令来查看所有可用的网络接口：

```bash
ifconfig -a
```
或者

```bash
ip link show
```
找到你想要配置的网络接口的名称，例如  eth0 。 
 
#### 步骤2：编辑interfaces文件 
 
编辑 /etc/network/interfaces 文件：

```bash
sudo vim /etc/network/interfaces
```
在文件中找到与你的网络接口相关的部分，或者如果还没有，就添加新的配置。例如，如果你的接口名称是  eth0 ，并且你想要设置的静态IP地址是  192.168.1.100 ，子网掩码是  255.255.255.0 ，默认网关是  192.168.1.1 ，那么你应该这样配置：
```bash

auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
 ```

#### 步骤3：重启网络服务 
 
配置完成后，你需要重启网络服务来应用更改。在 Debian 和 Ubuntu 系统中，可以使用以下命令：

```bash
sudo /etc/init.d/networking restart
```
或者

```bash
sudo service networking restart
```
#### 步骤4：验证配置 
 
重启网络服务后，使用以下命令来确认IP地址已正确配置：

```bash
ifconfig eth0
````
或者

```bash
ip addr show eth0
```
你应该会看到你配置的静态 IP 地址已经被分配到 eth0 网络接口上。 