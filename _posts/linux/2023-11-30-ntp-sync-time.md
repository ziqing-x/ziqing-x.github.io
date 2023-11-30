---
title: 嵌入式 linux 使用 ntp 同步时间
tags: [arm]
category: linux
image:
  path: /assets/img/headers/ntp.webp
---

NTP，全称网络时间协议(Network Time Protocol)，是用来同步网络中各个计算机的时间的协议。它能够提供高精度的时间校准，使得网络中的计算机能够保持准确的时间。NTP 使用 UDP 作为其传输协议，端口号为123。

## 获取源码

直接下载即可。
```bash
https://www.ntp.org
```

## 交叉编译

```bash
cd ntp
export LD=arm-linux-gnueabihf-ld
export CC=arm-linux-gnueabihf-gcc
# 自行替换 openssl 的路径
./configure --host=arm-linux-gnueabihf --target=arm-linux --disable-shared --with-yielding-select=no --with-openssl-libdir=/home/linux/openssl-1.0.1f/install/lib --with-openssl-incdir=/home/linux/openssl-1.0.1f/install/include
make -j$(nproc)
```

## 使用

```bash
# 同步时间服务器的时间
./ntpdate target ip

```
## 注意事项

如果你同步完时间后发现时间相差8小时，修改时区即可，步骤如下：
```bash
# 1、打开 linux的 配置文件
vim /etc/profile
# 2、添加如下内容到文件末，然后重启设备即可
export TZ="UTC-08:00"

```