---
title: 交叉编译 iperf3
tags: [arm]
category: linux
---

## 一、获取源码
iperf 代码托管在 github，直接clone即可
```bash
git clone https://github.com/esnet/iperf.git
```

## 二、交叉编译

```bash
cd iperf
export LD=arm-linux-gnueabihf-ld
export CC=arm-linux-gnueabihf-gcc
mkdir output
./configure --host=arm-linux-gnueabihf --prefix=./output --target=arm-linux --disable-shared
make -j$(nproc) && make install
ls ./output/bin/
```
