---
title: std::condition_variable 简单用法
tags: [条件锁]
category: c++
---

## 一、简介
std::condition_variable 是一个条件变量，常用于以下场景，某一个线程因为某个条件未满足时可以使用条件变量使该线程处于阻塞状态，一旦条件满足，被阻塞的线程可以被唤醒继续运行；条件变量只能与互斥锁配合使用，常用的成员函数如下：
```c++
wait(); // 阻塞当前线程直到条件满足被唤醒
wait_for(); // 阻塞当前线程，直到唤醒条件变量或在指定的超时时间之后
wait_until(); // 阻塞当前线程，直到唤醒条件变量或直到达到指定的时间点为止
notify_one(); // 通知一个正在等待的线程
notify_all(); // 通知所有正在等待的线程
```

## 二、例子
```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>

std::mutex mtx;
std::condition_variable cv;

void func() {
    std::unique_lock<std::mutex> ulck(mtx);
    std::cout << "子线程等待中..." << std::endl;
#if 1 // 一直阻塞等待
    cv.wait(ulck);
#elif 0// 只等待20秒
    cv.wait_for(ulck, std::chrono::seconds(20) == std::cv_status::timeout);
#else // 等到指定的时间点
    cv.wait_until(ulck, std::chrono::system_clock::now() + std::chrono::seconds(1));
#endif
    std::cout << "子线程等待结束" << std::endl;
}

int main() {
    std::cout << "创建子线程" << std::endl;
    std::thread t(func);
    std::this_thread::sleep_for(std::chrono::seconds(3));
    std::cout << "子线程可以往下执行" << std::endl;
    cv.notify_one();
    t.join();

    return 0;
}
```