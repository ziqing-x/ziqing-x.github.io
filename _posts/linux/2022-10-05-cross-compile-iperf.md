---
title: 交叉编译 iperf
tags: [arm]
category: linux
image:
  path: /assets/img/headers/iperf.webp
---

iPerf是一个开源的网络性能测试工具，用于测量网络带宽、延迟和数据包丢失等指标。它可以在客户端和服务器之间进行测试，支持TCP和UDP协议。iPerf具有丰富的参数选项，可以模拟不同的网络场景和测试需求。它被广泛用于网络工程、系统管理和网络性能优化领域。

## 获取源码
iperf 代码托管在 github，直接clone即可，
```bash
git clone https://github.com/esnet/iperf.git
```
也通过下面这个链接下载

```bash
https://downloads.es.net/pub/iperf/
```

## 交叉编译

```bash
cd iperf
export LD=arm-linux-gnueabihf-ld
export CC=arm-linux-gnueabihf-gcc
mkdir output
./configure --host=arm-linux-gnueabihf --prefix=./output --target=arm-linux --disable-shared
make -j$(nproc) && make install
ls ./output/bin/
```
