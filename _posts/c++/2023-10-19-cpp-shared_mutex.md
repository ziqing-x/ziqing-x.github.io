---
title: 使用 c++ 的条件变量实现一个共享锁
tags: [共享锁]
category: c++
---

## 一、简介
读写锁又叫多读单写锁 (multi-reader single-writer lock)，所有线程可以同时读，但是只有一个线程可以单独写。

## 二、例子
```c++
#include <condition_variable>
#include <mutex>

class SharedMutex
{
public:
    SharedMutex() = default;
    SharedMutex(const SharedMutex &) = delete;
    SharedMutex &operator=(const SharedMutex &) = delete;
    ~SharedMutex() = default;
    void read_lock()
    {
        std::unique_lock<std::mutex> lock(mutex_);
        cv_.wait(lock, [this] { return 0 == exclusive_num_; });
        ++shared_num_;
    }
    void read_unlock()
    {
        std::unique_lock<std::mutex> lock(mutex_);
        --shared_num_;
        if (0 == shared_num_)
        {
            cv_.notify_one();
        }
    }
    void write_lock()
    {
        std::unique_lock<std::mutex> lock(mutex_);
        cv_.wait(lock, [this] { return 0 == shared_num_ && 0 == exclusive_num_; });
        exclusive_num_ = 1;
    }

    void write_unlock()
    {
        std::unique_lock<std::mutex> lock(mutex_);
        exclusive_num_ = 0;
        cv_.notify_all();
    }

private:
    int shared_num_{0};
    int exclusive_num_{0};
    std::mutex mutex_;
    std::condition_variable cv_;
};

template<typename T>
class ReadLock
{
public:
    explicit ReadLock(T &mutex)
        : mutex_(mutex)
    {
        mutex_.read_lock();
    }
    ReadLock(const ReadLock &) = delete;
    ReadLock &operator=(const ReadLock &) = delete;
    ~ReadLock()
    {
        mutex_.read_unlock();
    }

private:
    T &mutex_;
};

template<typename T>
class WriteLock
{
public:
    explicit WriteLock(T &mutex)
        : mutex_(mutex)
    {
        mutex_.write_lock();
    }
    WriteLock(const WriteLock &) = delete;
    WriteLock &operator=(const WriteLock &) = delete;
    ~WriteLock()
    {
        mutex_.write_unlock();
    }

private:
    T &mutex_;
};


// Usage

#include <iostream>
#include <thread>

SharedMutex shared_mutex;
std::thread t1, t2, t3;
int A = 0;

int main(int argc, char **argv)
{
    t1 = std::move(std::thread([&]() {
        while (true)
        {
            {
                ReadLock<SharedMutex> lck(shared_mutex);
                std::cout << "Thread1: read A : " << A << std::endl;
            }
            std::this_thread::sleep_for(std::chrono::seconds(1));
        }
    }));
    t2 = std::move(std::thread([&]() {
        while (true)
        {
            {
                ReadLock<SharedMutex> lck(shared_mutex);
                std::cout << "Thread2: read A : " << A << std::endl;
            }
            std::this_thread::sleep_for(std::chrono::seconds(1));
        }
    }));
    t3 = std::move(std::thread([&]() {
        while (true)
        {
            std::this_thread::sleep_for(std::chrono::seconds(1));
            {
                WriteLock<SharedMutex> lck(shared_mutex);
                std::cout << "Thread3: write A: " << ++A << std::endl;
            }
        }
    }));

    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```