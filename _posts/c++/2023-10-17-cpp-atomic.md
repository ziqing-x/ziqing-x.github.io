---
title: 采用 std::atomic 实现自旋锁
tags: [自旋锁]
category: c++
---

## 一、简介
自旋锁的介绍在前面的文章中已详细介绍过，这里就不重复介绍了。[传送门](https://github.com/ziqing-x/ziqing-x.github.io/_posts/c++/2023-10-16-cpp-atomic_flag.md)

## 二、例子
```c++
#include <atomic>

class SpinLock
{
public:
    SpinLock()
        : flag_(false)
    {}
    ~SpinLock() = default;

    void lock()
    {
        bool expect = false;
        while (!flag_.compare_exchange_weak(expect, true))
        {
            expect = false;
        }
    }

    void unlock()
    {
        flag_.store(false);
    }

private:
    std::atomic<bool> flag_;
};

#include <iostream>
#include <thread>

SpinLock slck;

int main()
{
    std::thread t1([&]() {
        while (true)
        {
            slck.lock();
            std::cout << "线程1持有锁\n";
            slck.unlock();
        }
    });

    std::thread t2([&]() {
        while (true)
        {
            slck.lock();
            std::cout << "线程2持有锁\n";
            slck.unlock();
        }
    });
    t1.join();
    t2.join();

    return 0;
}
```
