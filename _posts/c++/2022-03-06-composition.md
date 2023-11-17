---
title: 什么是组合?
tags: [类图]
category: c++
date: 2023-03-06 19:00:00 +0800
---

## 一、概念

如果一个或多个类的实例化对象是另一个类的成员，这种结构称为组合，组合是一种强关联关系，这些组合的类生命周期一样。

## 二、例子

```c++
#include <iostream>

class Network {
public:
    void connect() {
        std::cout << "Connecting" << std::endl;
    }
};

class App {
public:
    void run() {
        network_.connect();
        std::cout << "Runnig" << std::endl;
    }
private:
    Network network_; // 对象成员
};

int main() {
    App app;
    app.run();

    return 0;
}
```