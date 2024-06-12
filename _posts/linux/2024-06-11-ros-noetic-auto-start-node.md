---
title: ROS1 开机自启动节点
tags: [ros1]
category: linux
image:
  path: /assets/img/headers/ros1.webp
---

通常情况下，使用 ros-noetic-robot-upstart 就可以实现开机自启动，但是最近项目中发现有的节点通过 ros-noetic-robot-upstart 添加成服务后启动不了，所以自己手动撸了一个 (亲测有效)，记录一下。

## 创建启动脚本

`touch /home/xxx/start.sh`
`sudo chmod +x /home/xxx/start.sh`
`vim /home/xxx/start.sh`

输入如下内容

```bash
#!/bin/bash
source /opt/ros/noetic/setup.bash
source /home/xxx/catkin_ws/devel/setup.bash
roslaunch my_pkg app.launch
exit 0
```

## 创建服务

`sudo vim /lib/systemd/system/app.service`

输入如下内容

```bash
##############################################################
# sudo systemctl daemon-reload
# 管理服务 [使能自启动|启动|停止|重启|查看状态]
# sudo systemctl [enable|start|stop|restart|status] xxx.service
###############################################################

[Unit]
Description=app
After=network.target

[Service]
Type=simple
WorkingDirectory=/home/xxx
Environment=LD_LIBRARY_PATH=/opt/ros/noetic/lib:$LD_LIBRARY_PATH
ExecStart=/bin/su - xxx -c "/home/xxx/start.sh"

[Install]
WantedBy=multi-user.target
```

## 打开自启动

```bash
# 更新服务
sudo systemctl daemon-reload
# 打开自启动
sudo systemctl enable app.service
# 启动
sudo systemctl start app.service
# 查看状态
sudo systemctl status app.service
```



