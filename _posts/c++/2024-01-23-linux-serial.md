---
title: linux 环境下使用 c++ 实现读写串口代码
tags: [串口, serial]
category: c++
image:
    path: /assets/img/headers/cpp-serial.webp
---

嵌入式开发过程中，在 Linux 环境下通过串口读写数据是比较常见的场景，今天分享一下我平时使用的串口读写静态接口，不同于之前用rust实现的那个，这次用 c/c++ 实现。

## 代码

serial.cpp

```cpp
/*
 * @Author: 熊义 JassimXiong@gmail.com
 * @Date: 2024-01-23 08:01:18
 * @LastEditors: xiongyi jassimxiong@gmail.com
 * @LastEditTime: 2024-01-23 09:21:54
 * @FilePath: serial.cpp
 * @Description:
 *
 * Copyright (c) 2024 xxxx, 版权所有
 */

#include "serial.h"
#include "logger.h"

#include <errno.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>
namespace Serial {
    int open(const char *port_name) {
        auto fd = ::open(port_name, O_RDWR | O_NOCTTY | O_NDELAY); // 非阻塞
        if (fd < 0) {
            LOGE("open_port: Unable to open {}", port_name);
            return -1;
        }
        return fd;
    }

    int close(int fd) { return ::close(fd); }

    int set(int fd, speed_t speed, int bits, char event, int stop) {
        struct termios options;
        if (tcgetattr(fd, &options) != 0) {
            LOGE("set_option: Unable to get termios");
            return -1;
        }
        options.c_cflag |= (CLOCAL | CREAD); //(本地连接（不改变端口所有者)|接收使能)
        options.c_cflag &= ~CSIZE;           //屏蔽字符大小位
        options.c_cflag &= ~CRTSCTS;         //不使用流控制
        switch (bits) {
        case 7:
            options.c_cflag |= CS7;
            break;
        case 8:
            options.c_cflag |= CS8;
            break;
        default:
            LOGE("Unsupported data size");
            return -1;
        }
        switch (event) {
        case 'O': // 奇校验
            options.c_cflag |= PARENB;
            options.c_cflag |= PARODD;
            options.c_iflag |= (INPCK | ISTRIP);
            break;
        case 'E': // 偶校验
            options.c_iflag |= (INPCK | ISTRIP);
            options.c_cflag |= PARENB;
            options.c_cflag &= ~PARODD;
            break;
        case 'N': // 无校验
            options.c_cflag &= ~PARENB;
            break;
        default:
            LOGE("Unsupported parity");
            return -1;
        }
        if (stop == 1) {
            options.c_cflag &= ~CSTOPB;
        } else if (stop == 2) {
            options.c_cflag |= CSTOPB;
        } else {
            LOGE("Unsupported stop bits");
            return -1;
        }
        options.c_iflag &= ~(ICRNL | IXON); //~(将CR映射到NL|启动出口硬件流控)
        options.c_oflag = 0;                //输出模式标志
        options.c_lflag = 0;                //本地模式标志
        cfsetispeed(&options, speed);       //设置输入速度,波特率
        cfsetospeed(&options, speed);       //设置输出速度,波特率
        __attribute__((unused)) auto ret = tcsetattr(fd, TCSANOW, &options); //设置属性(termios结构)
        return 0;
    }

    int read(int fd, char *buf, int len, int timeout /*毫秒*/) {
        int ret = 0;
        int ava = 0;
        fd_set read_fds;
        struct timeval tv;
        tv.tv_sec = timeout / 1000;           // seconds
        tv.tv_usec = (timeout % 1000) * 1000; // microseconds
        while ((len - ava) > 0) {
            FD_ZERO(&read_fds);
            FD_SET(fd, &read_fds);
            ret = select(fd + 1, &read_fds, NULL, NULL, &tv);
            LOGD("select return: {}", ret);
            if (ret < 0) {
                LOGD("select errno={}", errno);
                // 错误码为11表示重试
                if (errno == 11) {
                    continue;
                } else {
                    ava = ret;
                    break;
                }
            } else if (ret == 0) {
                // 超时
                break;
            } else {
                ret = ::read(fd, buf + ava, len - ava);
                if (ret < 0) {
                    ava = ret;
                    break;
                }
                ava += ret;
            }
        }
        return ava;
    }

    int write(int fd, char *buf, int len) {
        int ret = 0;
        int ava = 0;
        if (buf == NULL || len <= 0) {
            return -1;
        }
        // 清空串口的接收缓冲数据和发送缓冲数据
        tcflush(fd, TCIOFLUSH);
        while ((len - ava) > 0) {
            ret = ::write(fd, buf + ava, len - ava);
            if (ret < 0) {
                LOGD("::write errno={}", errno);
                if (errno == 11) {
                    continue;
                } else {
                    ava = ret;
                    break;
                }
            }
            ava += ret;
        }
        return ava;
    }

} // namespace Serial
```

serial.h

```cpp
/*
 * @Author: xiongyi jassimxiong@gmail.com
 * @Date: 2024-01-23 07:51:20
 * @LastEditors: xiongyi jassimxiong@gmail.com
 * @LastEditTime: 2024-01-23 09:53:03
 * @FilePath: serial.h
 * @Description:
 *
 * Copyright (c) 2024 xxxx, 版权所有
 */

#ifndef __SERIAL_H__
#define __SERIAL_H__

#include <termios.h>

namespace Serial {
    /**
     * @brief 打开串口，返回文件描述符
     * @param port_name   串口名
     * @return int        文件描述符： -1:fail others:fd
     */
    int open(const char *port_name);

    /**
     * @description: 关闭串口
     * @param {int} fd
     * @return {*}
     */
    int close(int fd);

    /**
     * @brief 设置串口通信参数
     * @param fd       串口文件描述符
     * @param speed   波特率： B4800、B9600、B115200....etc
     * @param bits    数据位： 7、8
     * @param event   奇偶校验位: 'N':None、'O':Odd、'E':Even
     * @param stop    停止位: 1、2
     * @return int     0:success，-1:fail
     */
    int set(int fd, speed_t speed, int bits, char event, int stop);

    /**
     * @brief 读取串口数据
     * @param fd        串口文件描述符
     * @param buf       存放数据内容
     * @param len       需要读取的数据长度
     * @param timeout   超时时间,单位毫秒
     * @return int      读取回来的可用数据长度
     */
    int read(int fd, char *buf, int len, int timeout);

    /**
     * @description: 向串口写入数据，将buf缓冲区中的数据写入串口，返回实际写入的字节数。
     * @param {int} fd
     * @param {char} *buf
     * @param {int} len
     * @return {*}
     */
    int write(int fd, char *buf, int len);
} // namespace Serial
#endif // __SERIAL_H__
```