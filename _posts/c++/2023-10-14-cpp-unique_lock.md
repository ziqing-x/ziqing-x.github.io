---
title: std::unique_lock 简单用法
tags: [互斥锁]
category: c++
---

## 一、简介
前面我们有讲到 std::lock_guard ，它可以在构造函数里自动调用 lock 函数, 在析构函数里自动 unlock ，使得我们不必担心 忘记 unlock 的情况，但是有时候在函数的执行过程中，我们可能需要提前 unlock ，然后再 lock ,最后函数返回的时候让 unlock自动被调用，这个时候 std::lock_guard 就没那么灵活了，而 std::unique_lock 的出现可以完美的解决这个问题。

## 二、例子
```c++
#include <mutex>
#include <iostream>

std::mutex mtx;
bool flag = false;

void test_lock() {
    std::unique_lock<std::mutex> ulck(mtx);
    // ulck 构造函数里自动 lock ，防止多个线程对这个值同时修改
    flag = true;
    // 下面的代码不需要互斥访问时，提前unlock
    ulck.unlock();
    // do nothing
    // 如果又出现需要互斥访问的代码片段时，也可以再次 lock
    // 最后 ulck 出作用域时会在析构函数里 unlock
}
int main() {
    test_lock();
    return 0;
}
```