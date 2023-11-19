---
title: std::recursive_mutex 简单用法
tags: [递归锁]
category: c++
image:
    path: /assets/img/headers/recursive_mutex.webp
---

std::recursive_mutex 是 c++ 标准库中的可递归互斥量，它与 std::mutex 类似，但允许同一个线程多次对互斥量进行加锁操作，而不会导致死锁。这意味着线程可以在持有互斥量的情况下再次对其进行加锁，而不会被阻塞。递归互斥量非常适用于需要在嵌套函数或递归调用中使用互斥量的情况，确保线程安全性的同时保持灵活性。话不多说，看下面这种情况:

```c++
std::mutex mtx;

void func1()
{
    mtx.lock();
    // do something
    mtx.unlock();
}

void func2()
{
    mtx.lock();
    func1();
    // do something
    mtx.unlock();
}
```
当出现上面这种情况的时候，就会陷入死锁，递归锁就可以解决这个问题，它允许在同一线程反复加锁，只要加锁次数和解锁次数相等就不会陷入死锁。

## 示例

```c++
#include <mutex>
#include <thread>
#include <iostream>

std::recursive_mutex rmtx;

void func1()
{
    mtx.lock();
    std::cout << "func1" << std::endl;
    mtx.unlock();
}

void func2()
{
    mtx.lock();
    std::cout << "func2" << std::endl;
    func1();
    mtx.unlock();
}

int main()
{
    std::thread t1(func1);
    std::thread t2(func2);

    t1.join();
    t2.join();

    return 0;
}
```