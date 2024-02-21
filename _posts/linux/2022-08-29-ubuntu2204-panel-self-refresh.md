---
title: 如何解决新买的联想 thinkbook14 重装 ubuntu22.04 后显卡不正常问题
tags: [PSR]
category: linux
image:
  path: /assets/img/headers/thinkbook.webp
---

最近买了一台联想的 thinkbook14 笔记本，重装了 ubuntu22.04 后，发现显卡异常，几经周折，终于解决了，下面就把这个方法分享给大家。

## 现象

开机进入系统后，屏幕闪烁、花屏、或者卡顿。

## 原因

ubuntu22.04 开机时默认启用面板自刷新（Panel Self Refresh，PSR）功能。PSR是一种节能技术，但在某些情况下可能会导致显示问题。 

## 解决办法

禁用面板自刷新功能即可。
打开配置文件
```bash
sudo gedit /etc/default/grub
```

更改 GRUB_CMDLINE_LINUX_DEFAULT 行为如下内容

```bash
GRUB_CMDLINE_LINUX_DEFAULT=“quiet splash i915.enable_psr=0”
```

更新 grub 并重启

```bash
sudo update-grub
sudo reboot
```
## 注意事项

网上有的帖子是改成 nomodeset，如果改成 nomodeset，花屏确实解决了，但是无法调整亮度，显卡信息也不对，所以还是改成 i915.enable_psr=0 才能完美解决。


