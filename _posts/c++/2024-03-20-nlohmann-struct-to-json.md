---
title: c++ 中 struct 如何方便快捷地转成 json
tags: [json, nlohmann]
category: c++
image:
    path: /assets/img/headers/nlohmann.webp
---

在开发过程中常需要把结构体和 json 相互转换，如果 json 字段较多，简直就是灾难，要写很多重复的、类似的代码，下面推荐一种 struct 转 json 的简便方法。采用 nlohmann_json 实现（不作介绍，自行去 github 查看详情）。

## 示例
创建一个 test.h 文件，写入下面的代码

```c++
#ifndef __TEST__H__
#define __TEST__H__

#include "nlohmann/json.hpp"
#include <string>

struct Person {
    std::string name;
    int age; 
};

NLOHMANN_DEFINE_TYPE_NON_INTRUSIVE(Person, age, name)
#endif // __TEST__H__
```

创建一个 test.cpp文件， 写入下面的代码

```c++
#include "test.h"
#include <iostream>

using Json = nlohmann::json;

void test() {
    Person me {"yuqing", 27};
    Json json;
    to_json(json, me);
    std::cout << json.dump(4) << "\n";
}
```

## 总结
在上面的代码中，有如下几个需要简要介绍一下：
+ 1、在 NLOHMANN_DEFINE_TYPE_NON_INTRUSIVE(Person, age, name) 中， Person 是结构体名， age 和 name 是结构体成员，可以交换结构体成员的位置， 例如：NLOHMANN_DEFINE_TYPE_NON_INTRUSIVE(Person, name, age) 也是可以的，等效于前者。
+ 2、可以连续声明多个结构体，并对该结构体使用 NLOHMANN_DEFINE_TYPE_NON_INTRUSIVE 宏；
+ 3、NLOHMANN_DEFINE_TYPE_NON_INTRUSIVE 宏的本质是编译时自动展开生成两个函数, 例如对于 Person，相当于生成了下面两个函数：

```c++
void to_json(Json& j, const Person& p);
void from_json(const Json& j, Person& p);

```
+ 4、json.dump(4) 会把 json 转化成 string, 并且以 tap = 4 的方式格式化，例如上面最终的输出结果为:

```json
{
    "name":"yuqing",
    "age": 27
}
```
如果是 json.dump() 则输出如下字符串:

```json
{"name":"yuqing","age":27}

```