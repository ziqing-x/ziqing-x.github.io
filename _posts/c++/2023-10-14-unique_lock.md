---
title: std::unique_lock 简单用法
tags: [互斥锁]
category: c++
image:
    path: /assets/img/headers/unique_lock.webp
---

std::unique_lock 是 c++ 标准库中的互斥锁封装类，与 std::lock_guard 类似，也可以自动管理互斥量的上锁和解锁操作，但提供了更高级的锁定机制，支持可延迟和可中断的锁定、多个互斥量的锁定等特性，更加灵活和可控。

## 示例

```c++
#include <mutex>
#include <iostream>

std::mutex mtx;
bool flag = false;

void test_lock()
{
    std::unique_lock<std::mutex> ulck(mtx);
    // ulck 构造函数里自动 lock ，防止多个线程对这个值同时修改
    flag = true;
    // 下面的代码不需要互斥访问时，提前unlock
    ulck.unlock();
    // do nothing
    // 如果又出现需要互斥访问的代码片段时，也可以再次 lock
    // 最后 ulck 出作用域时会在析构函数里 unlock
}
int main()
{
    test_lock();
    return 0;
}
```