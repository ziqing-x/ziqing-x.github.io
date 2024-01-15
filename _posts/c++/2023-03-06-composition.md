---
title: 什么是组合?
tags: [UML]
category: c++
image:
    path: /assets/img/headers/uml.webp
---

在 UML 中，组合是一种关系，表示一个对象包含另一个对象，并且两者的生命周期是紧密相关的。

## 示例

```c++
#include <iostream>

class Network
{
private:
    Network network_; // 对象成员

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
        network_.connect();
        std::cout << "Runnig" << std::endl;
    }
};

int main()
{
    App app;
    app.run();

    return 0;
}
```