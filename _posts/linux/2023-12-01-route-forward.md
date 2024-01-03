---
title: linux 下实现双网卡路由转发
tags: [路由转发]
category: linux
image:
  path: /assets/img/headers/route.webp
---

想象如下这个场景，一个 linux 设备上有两个网卡，网卡一和网卡二分别处于两个不同的网段，并与其他设备相连，如何让连接 网卡一的设备和连接网卡二的设备网络互通？ 下面介绍如何实现这个需求。

## 启用网络转发
vi /etc/sysctl.conf
```bash
net.ipv4.ip_forward=1
```

## 使配置生效
```bash
sysctl -p
```

## 使用iptables配置规则
```bash
iptables -F
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```

## 配置转发
```bash
# 在 B 中配置转发，把 192.168.2.0 网段的数据包转发到网卡二 192.168.3.22
iptables -t nat -A POSTROUTING -s 192.168.2.0/24 -o 网卡二 -j SNAT --to-source 192.168.3.22
# 在 A 中添加 B 的网卡一的 ip 作为默认网关
route add default gw 192.168.2.11
# 在 C 中添加 B 的网卡二的 ip 作为默认网关
route add default gw 192.168.3.22
```

## 测试
A ping C，能 ping 通即可