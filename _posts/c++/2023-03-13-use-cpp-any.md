---
title: std::any简单用法
tags: [any]
category: c++
date: 2023-03-13 16:00:00 +0800
---

## 一、概念
std::any是C++17中引入的一个类模板，它允许存储和检索任意类型的值，提供了一种类型安全的、动态的值存储和访问机制。

## 二、例子
```c++
#include <iostream>
#include <any>

struct Person {
    std::string name;
    int age;
};


int main() {
    std::any var = 88;
    std::cout << std::any_cast<int>(var) << "\n";

    std::any str = "hahaha";
    std::cout << std::any_cast<std::string>(str) << "\n";
    
    Person p = {"老王", 88};
    std::any info = p;
    auto ret = std::any_cast<Person>(info);
    std::cout << "姓名: " 
            <<ret.name 
            << " 年龄: "
            << ret.age
            <<"\n";
    return 0;
}
```


