---
title: std::variant 简单用法
tags: [variant]
category: c++
image:
    path: /assets/img/headers/variant.webp
---

std::variant 是 c++17 引入的标准库类，它提供了一种类型安全的机制，可以在编译时定义一个可以存储多种类型的变量。通过std::variant，我们可以在运行时将值存储在一个可变的、类型安全的容器中，并在需要时进行类型转换和访问，从而实现更灵活的编程。和 union 类似。

## 示例

```c++
#include <iostream>
#include <variant>
#include <string>
#include <vector>

struct MyType
{
    std::string name;
    int age;
};

using Value = std::variant<int, double, std::string, bool, MyType>;

struct Print
{
    void operator()(int i)
    {
        std::cout << i << std::endl;
    }
    void operator()(double i)
    {
        std::cout << i << std::endl;
    }
    void operator()(std::string i)
    {
        std::cout << i << std::endl;
    }
    void operator()(bool i)
    {
        std::cout << i << std::endl;
    }
    void operator()(MyType i)
    {
        std::cout << i.name << i.age << std::endl;
    }
};

int main()
{
    MyType t;
    t.name = "xiong";
    t.age = 27;
    std::vector<Value> test = {
        88,
        99.9,
        "Hello world",
        true,
        t
    };
    for (const auto &it : test)
    {
        std::cout << it.index() << std::endl;
        std::visit(Print{}, it);
    }
    std::cout << std::get<0>(test[0]) << std::endl;
    std::cout << std::get<1>(test[1]) << std::endl;
    std::cout << std::get<2>(test[2]) << std::endl;
    std::cout << std::get<3>(test[3]) << std::endl;
    std::cout << std::holds_alternative<std::string>(test[2]) << std::endl;

    return 0;
}
```