---
title: 禁止 ubuntu 自动更新的方法
tags: [upgrade,update]
category: linux
image:
  path: /assets/img/headers/upgrade.webp
---

最近在使用 ubuntu 开发时，经常会出现开机后系统自动更新，导致某些驱动不能正常工作。这种情况只要禁止系统自动更新, 重新编译并安装驱动就解决了。下面是如何禁止自动更新的步骤。

## 步骤

1、 `sudo vi /etc/apt/apt.conf.d/10periodic`
后面部分全部改成 “0”

```bash
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Download-Upgradeable-Packages "0";
APT::Periodic::AutocleanInterval "0";
```

2、`sudo vi /etc/apt/apt.conf.d/20auto-upgrades`
后面部分全部改成 “0”

```bash
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Unattended-Upgrade "0";
```

3、重启