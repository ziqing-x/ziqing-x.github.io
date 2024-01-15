---
title: 什么是聚合?
tags: [UML]
category: c++
image:
    path: /assets/img/headers/uml1.webp
---

在 UML 中，聚合是一种关系，表示一个对象包含另一个对象，但两者的生命周期可以独立存在。

## 示例

```c++
#include <iostream>

class Network
{
private:
    Network *network_; // 指针成员

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
};

int main()
{
    App app;
    app.run();

    return 0;
}
```