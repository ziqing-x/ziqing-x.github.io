---
title: std::variant 简单用法
tags: [variant]
category: c++
---

## 一、简介
std::variant 和std::any 算是兄弟关系，和 std::any 一样，也是c++17引入标准库的，和 union 类似。

## 二、例子
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