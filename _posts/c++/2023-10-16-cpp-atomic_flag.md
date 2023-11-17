---
title: 采用 std::atomic_flag 实现自旋锁
tags: [自旋锁]
category: c++
---

## 一、简介
+ 自旋锁属于 busy-waiting 类型锁，它避免了操作系统进程调度和线程切换开销(用户进程和内核切换的消耗)，通常适用在时间极短的情况，所以如果一个线程持有锁的时间比较短，那么就可以使用自旋锁; 操作系统的内核经常使用自旋锁。
+ 但如果长时间上锁，自旋锁会非常耗费性能。线程持有锁时间越长，则持有锁的线程被 OS调度程序中断的风险越大，如果发生中断情况，那么其它线程将保持旋转状态(反复尝试获取锁)，而持有锁的线程并不打算释放锁，导致的结果是无限期推迟，直到持有锁的线程可以完成并释放它为止。
+ 持有自旋锁的线程在sleep之前应该释放自旋锁以便其他线程可以获得该自旋锁。实际上许多其他类型的锁在底层使用了自旋锁实现，例如多数互斥锁在试图获取锁的时候会先自旋一小段时间，然后才会休眠。如果在持锁时间很长的场景下使用自旋锁，则会导致CPU在这个线程的时间片用尽之前一直消耗在无意义的忙等上，造成计算资源的浪费。

## 二、例子
```c++
#include <atomic>

class SpinLock {
private:
  std::atomic_flag flag_;

public:
  SpinLock() : flag_(ATOMIC_FLAG_INIT) {}
  ~SpinLock() = default;
  void lock() {
    // 获得锁 (test_and_set为设置当前lock为true，并返回lock设置之前的值)
    while (flag_.test_and_set(std::memory_order_acquire));
  }
  void unlock() {
    // 释放锁 (clear为设置lock的值为false)
    flag_.clear(std::memory_order_release);
  }
};

#include <iostream>
#include <thread>

SpinLock slck;

int main() {
  std::thread t1([&]() {
    while (true) {
      slck.lock();
      std::cout << "线程1持有锁\n";
      slck.unlock();
    }
  });

  std::thread t2([&]() {
    while (true) {
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