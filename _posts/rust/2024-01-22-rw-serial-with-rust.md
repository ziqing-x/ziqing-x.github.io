---
title: 使用 rust 读写串口
tags: [串口, serial]
category: rust
image:
    path: /assets/img/headers/rust-serial.webp
---

在嵌入式开发中经常使用到串口，今天准备演示一下如何使用 rust 来编写访问串口，下面以某公司的电池控制为例 一起来感受一下 rust 惊人的开发效率吧！我们使用 [serialport](https://crates.io/crates/serialport) 来实现，话不多说，上干货！

## 创建项目

```bash
cargo new serial_demo
```

## 添加依赖

在 Cargo.toml 文件里指定 serialport 的版本

```toml
[dependencies]
serialport = "4.3.0"
```

## 编写代码

```rust
use std::{thread::sleep, time::Duration};

use serialport::SerialPort;

fn main() -> serialport::Result<()> {
    // 列出所有可用的串行端口
    let ports = serialport::available_ports()?;
    for p in ports {
        println!("找到串行端口: {:?}", p);
    }

    // 实例化一个串口
    let mut port = serialport::new("/dev/ttyUSB0", 115_200)
    .timeout(Duration::from_millis(3000))
    .open().expect("Failed to open port");

    println!("获取电池状态:");
    let state: &[u8; 15] = &[0xAB, 0xCB, 0x00, 0x0B, 0x02, 0x01, 0x03, 0x01, 0x02, 0x03, 0x04, 0x01, 0x0B, 0x00, 0x27];
    port.write(state).expect("Write failed!");
    print_buf(&read_ack(& mut port, 66));


    println!("电池开机:");
    let on: &[u8; 15] =  &[0xAB, 0xCB, 0x00, 0x0B, 0x02, 0x01, 0x03, 0x01, 0x02, 0x03, 0x04, 0x01, 0x04, 0x00, 0x20];
    port.write(on).expect("Write failed!");
    print_buf(&read_ack(& mut port, 17));

    sleep(Duration::from_millis(3000));

    println!("电池关机:");
    let off: &[u8; 15] = &[0xAB, 0xCB, 0x00, 0x0B, 0x02, 0x01, 0x03, 0x01, 0x02, 0x03, 0x04, 0x01, 0x05, 0x00, 0x21];
    port.write(off).expect("Write failed!");
    print_buf(&read_ack(& mut port, 17));
    Ok(())
}

fn read_ack(port: &mut Box<dyn SerialPort>, len: usize) -> Vec<u8> {
    let mut read_buf: Vec<u8> = vec![0; len];
    port.read(read_buf.as_mut_slice()).expect("Found no data!");
    read_buf
}

fn print_buf(buf: &Vec<u8>) {
    for byte in buf {
        print!("{:02X} ", byte);
    }
    println!();
}

```