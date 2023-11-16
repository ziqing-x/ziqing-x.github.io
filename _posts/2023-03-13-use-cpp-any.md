---
title: c++17使用std::any
tags: [any]
category: c++
description: std::any简单用法
---

std::any是c++17引入标准库的，用来存放任意类型;

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


