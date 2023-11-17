---
title: 什么是聚合?
tags: [类图]
category: c++
---

## 一、简介
如果一个或多个类的指针对象是另一个类的成员，这种结构称为组合，组合是一种强关联关系，这些组合的类生命周期一样。

## 二、例子
```c++
#include <iostream>

class Network
{
public:
    void connect()
    {
        std::cout << "Connecting" << std::endl;
    }
};

class App
{
public:
    void run()
    {
        network_ = new Network;
        network_->connect();
        std::cout << "Runnig" << std::endl;
        delete network_;
    }

private:
    Network *network_; // 对象成员
};

int main()
{
    App app;
    app.run();

    return 0;
}
```