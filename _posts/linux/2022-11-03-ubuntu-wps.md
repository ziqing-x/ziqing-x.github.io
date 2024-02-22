---
title: ubuntu 下 wps 字体缺失解决办法
tags: [wps字体缺失]
category: linux
image:
  path: /assets/img/headers/wps.webp
---

在 ubuntu 系统上安装 wps 后，每次启动都会报错 `some formula symbols might be not display`，原因是 wps 字体缺失，解决办法如下：

```bash
git clone https://github.com/IamDH4/ttf-wps-fonts.git
cd ttf-wps-fonts
sudo ./install.sh
```