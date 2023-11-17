---
title: std::call_once简单用法示例
tags: [call_once]
category: c++
date: 2023-03-03 16:00:00 +0800
---

## 一、用途
在c++中有这么一个需求， 多个线程同时调用同一个函数，为确保函数或者代码片段只被调用一次，代码应该如何实现？ std::call_once，能很好的解决这个问题。

## 二、例子

```c++
#include <iostream>
#include <mutex>
#include <thread>

static std::once_flag flag;

void Test()
{
    std::call_once(flag, []() {
        // 该代码片段只会被调到一次
        std::cout << "I am Test" << std::endl;
      }
    );
}

int main()
{
    std::thread t1([&]() {
        while (true)
        {
            Test();
            std::this_thread::sleep_for(std::chrono::seconds(1));
        }
    });

    std::thread t2([&]() {
        while (true)
        {
            Test();
            std::this_thread::sleep_for(std::chrono::seconds(1));
        }
    });

    std::thread t3([&]() {
        while (true)
        {
            Test();
            std::this_thread::sleep_for(std::chrono::seconds(1));
        }
    });

    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```
