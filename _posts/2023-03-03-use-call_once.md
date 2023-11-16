---
title: std::call_once使用
tags: [call_once]
category: c++
description: std::call_once简单用法示例
---

## 前言

有这么一个需求， 有多个线程调用了同一个函数，我需要确保函数或代码片段在多线程环境下，只需要执行一次（例如懒汉式单例），怎实现？ c++11引入了std::call_once，能很好的解决这个问题。

## 头文件

```c++
#inlcude <mutex>
```

## 例子

```c++
#include <iostream>
#include <mutex>
#include <thread>

static std::once_flag flag;

void Test() { std::cout << "I am Test\n"; }

int main() {
  std::thread t1([&]() {
    while (true) {
      std::call_once(flag, [&]() { Test(); });
      std::this_thread::sleep_for(std::chrono::seconds(1));
    }
  });

  std::thread t2([&]() {
    while (true) {
      std::call_once(flag, [&]() { Test(); });
      std::this_thread::sleep_for(std::chrono::seconds(1));
    }
  });

  std::thread t3([&]() {
    while (true) {
      std::call_once(flag, [&]() { Test(); });
      std::this_thread::sleep_for(std::chrono::seconds(1));
    }
  });

  if (t1.joinable()) {
    t1.join();
  }
  if (t2.joinable()) {
    t2.join();
  }
  if (t3.joinable()) {
    t3.join();
  }
  return 0;
}
```
