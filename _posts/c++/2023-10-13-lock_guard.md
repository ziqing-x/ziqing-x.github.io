---
title: std::lock_guard 简单用法
tags: [互斥锁]
category: c++
image:
    path: /assets/img/headers/lock_guard.webp
---

std::lock_guard 是 c++ 标准库中的 RAII 类模板，用于在作用域内自动管理互斥量的上锁和解锁操作，确保在离开作用域时正确释放互斥量，避免忘记解锁而导致的死锁问题。

## 示例

```c++
#include <mutex>
#include <iostream>

std::mutex mtx;

// 使用 lock_guard 前 unlock无法被调用
# if 0
void test_lock()
{
    mtx.lock();
    if (true)
    {
        std::cout << "exit \n";
        mtx.unlock();
        return;
    }
    mtx.unlock();
}
#endif

// 使用 lock_guard 后不用担心 未 unlock 的问题
void test_lock()
{
    std::lock_guard<std::mutex> glck(mtx);
    if (true)
    {
        std::cout << "exit" << std::endl;
        return;
    }
}

int main()
{
    test_lock();
    return 0;
}
```