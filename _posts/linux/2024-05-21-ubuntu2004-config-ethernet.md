---
title: 怎样给 Jetson Orin NX T801 的 eth0 配置静态 ip
tags: [ethernet, ubuntu]
category: linux
image:
  path: /assets/img/headers/jetson.webp
---

我这里有一块 Jetson Orin NX T801，它自带有 wifi 和 ethernet 接口，但是没有 USB 和 HDMI 接口，我们想给 eth 配置个静态 ip，试了多种办法都不行，下面分享一种比较可靠的方法。

## 安装 network-manager
```bash
sudo apt-get install network-manager
```

## 添加配置
假设 eth0 的 ip 地址要设置为 192.168.55.100，子网掩码为 255.255.255.0，网关为 192.168.55.1，DNS 为 8.8.8.8。按照如下命令添加配置即可：

```bash
sudo nmcli connection add type ethernet ifname eth0 con-name my_eth0 ipv4.method manual ipv4.address 192.168.55.100/24 ipv4.gateway 192.168.55.1
```

`注意`:
```bash
# 这是自动分配 IP 的方式
# sudo nmcli connection add type ethernet ifname eth0 con-name my_eth0 ipv4.method auto
```

## 自动连接

```bash
sudo nmcli connection modify usb0 connection.autoconnect yes
```

## 重启网络

```bash
sudo systemctl restart network-manager
```

## 验证

```bash
sudo ip addr show eth0
```

如果看到类似如下输出，说明配置成功：

```bash
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:00 brd ff:ff:ff:ff:ff:ff
    inet 192.168.55.100/24 brd 192.168.55.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe8a:1c2a/64 scope link
       valid_lft forever preferred_lft forever
```
