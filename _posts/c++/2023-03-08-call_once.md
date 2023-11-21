---
title: std::call_once 简单用法
tags: [call_once]
category: c++
image:
    path: /assets/img/headers/call_once.webp
---

std::call_once 是一个 c++11 标准库函数，用于保证多线程环境下某个函数只被调用一次。

## 示例

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
