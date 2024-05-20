---
title: 怎样通过命令行配置 ubuntu 的 wifi
tags: [wifi, ubuntu]
category: linux
image:
  path: /assets/img/headers/nmcli.webp
---

有时候我们不具备可视化的条件，只能通过终端控制 ubuntu 系统，这个时候如果想要去配置它，该怎么办？下面就分享一种相对简单的方法。

## 安装 network-manager 网络管理工具

```bash
sudo apt-get install network-manager
```

## 查看 wifi 列表

```bash
nmcli dev wifi list
```

## 连接 wifi

```bash
nmcli dev wifi connect WIFI_NAME password WIFI_PASSWORD
```

## 设置自动连接

```bash
nmcli con add con-name WIFI_NAME ifname wlan0 type wifi ssid WIFI_NAME
nmcli con up WIFI_NAME
```


