---
title: 以服务的形式开机自启动 roscore
tags: [ROS]
category: linux
image:
  path: /assets/img/headers/roscore.webp
---

为了让 ROS 节点在系统启动时自动启动，我们可以将 roscore 作为服务来实现。

## 创建服务

`sudo vim /lib/systemd/system/my-roscore.service`

输入如下内容

```bash
######################################################################
# sudo systemctl daemon-reload
# 管理服务 [使能自启动|启动|停止|重启|查看状态]
# sudo systemctl [enable|start|stop|restart|status] my-roscore.service
######################################################################

[Unit]
	Description=roscore
	After=network.target

[Service]
	Type=simple
	Restart=on-failure
	User=root
	Group=root
	Environment=ROS_MASTER_URI=http://localhost:11311
	ExecStart=/bin/bash -c 'source /opt/ros/noetic/setup.bash; roscore'

[Install]
	WantedBy=multi-user.target
```

## 服务管理

```bash
# 使配置生效
sudo systemctl daemon-reload
# 打开自启动
sudo systemctl enable my-roscore.service
# 关闭自启动
sudo systemctl disable my-roscore.service
# 启动
sudo systemctl start my-roscore.service
# 停止
sudo systemctl start my-roscore.service
# 重启
sudo systemctl restart my-roscore.service
# 查看状态
sudo systemctl status my-roscore.service
```



