---
title: c++17 的共享锁 std::shared_mutex
tags: [共享锁]
category: c++
image:
    path: /assets/img/headers/std_shared_mutex.webp
---

std::shared_mutex 是 c++17 引入的一个同步机制类，它允许多个线程同时读取共享数据，但在写入数据时要求独占访问权。这种读写锁的设计旨在提高在多线程环境下对共享数据进行读取操作时的效率，因为它减少了锁的竞争，允许更高的并发度。当某个线程需要写入时，它会等待所有读操作完成后才进行，确保数据一致性和线程安全。

## 示例

```c++
#include <any>
#include <iostream>
#include <mutex>
#include <shared_mutex>
#include <unordered_map>

class DataCenter {
private:
    std::unordered_map<std::string, std::any> datas_;
    mutable std::shared_mutex rw_mutex_;

public:
    DataCenter() {}
    ~DataCenter() {}

    template <typename ValueType>
    void put(const std::string &key, const ValueType &value) {
        std::unique_lock lock(rw_mutex_);
        datas_[key] = value;
    }

    template <typename ValueType>
    auto get(const std::string &key) const -> ValueType {
        std::shared_lock lock(rw_mutex_);
        if (datas_.count(key) == 0) {
            std::cout << "[DC] key not found, key: " << key << std::endl;
            return {};
        }
        try {
            return std::any_cast<ValueType>(datas_.at(key));
        } catch (...) {
            std::cout << "[DC] type error, key: " << key << std::endl;
            return {};
        }
    }

    void remove(const std::string &key) {
        std::unique_lock lock(rw_mutex_);
        datas_.erase(key);
    }
};
```