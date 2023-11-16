---
title: c++17使用std::variant
tags: [variant]
category: c++
description: std::variant简单用法
---

std::variant和std::any算是兄弟关系，和std::any一样，也是c++17引入标准库的，和Union类似，下面是使用的例子：
```c++
#include <iostream>
#include <variant>
#include <string>
#include <vector>

struct MyType{
    std::string name;
    int age;
};

using Value = std::variant<int, double, std::string, bool, MyType>;

struct Print{
    void operator()(int i ) {
        std::cout << i << "\n";
    }
    void operator()(double i) {
        std::cout << i << "\n";
    }
    void operator()(std::string i) {
        std::cout << i << "\n";
    }
    void operator()(bool i) {
        std::cout << i << "\n";
    }
    void operator()(MyType i) {
        std::cout << i.name << i.age << "\n";
    }
};


int main() {
    MyType t;
    t.name = "xiong";
    t.age = 27;
    std::vector<Value> test = {88, 99.9, "Hello world", true, t};
    for (auto it : test) {
        std::cout << it.index() << "\n";
        std::visit(Print{}, it);
    }
    std::cout << std::get<0>(test[0]) << "\n";
    std::cout << std::get<1>(test[1]) << "\n";
    std::cout << std::get<2>(test[2]) << "\n";
    std::cout << std::get<3>(test[3]) << "\n";
    std::cout << std::holds_alternative<std::string>(test[2]) << "\n";

    return 0;
}
```