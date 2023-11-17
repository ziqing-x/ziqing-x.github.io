---
title: std::recursive_mutex 简单用法
tags: [递归锁]
category: c++
---

## 一、简介
话不多说，看下面这种情况:
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

## 二、例子
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