---
title: std::lock_guard 简单用法
tags: [互斥锁]
category: c++
---

## 一、简介
你有没有遇到过这样一个问题, 有时候在使用 std::mutex 的过程中, 在函数最后 unlock ，但是由于函数中有太多条件判断，有时候函数中途 return 导致 unlock 没有被调到。std::lock_guard 就可以完美解决这个问题，通过函数的构造函数 lock，在析构的时候通过析构函数 unlock 。

## 二、例子
```c++
#include <mutex>
#include <iostream>

std::mutex mtx;

// 使用 lock_guard 前
// void test_lock() {
//     mtx.lock();
//     if (true) {
//         std::cout << "exit \n";
//         mtx.unlock();
//         return;
//     }
//     mtx.unlock();
// }

// 使用 lock_guard 后不用担心 未 unlock 的问题
void test_lock() {
    std::lock_guard<std::mutex> glck(mtx);
    if (true) {
        std::cout << "exit \n";
        return;
    }
}

int main() {
    test_lock();
    return 0;
}
```