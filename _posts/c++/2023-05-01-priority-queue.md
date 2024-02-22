---
title: c++ 优先级队列
tags: [队列, 优先级队列]
category: c++
image:
    path: /assets/img/headers/priority_queue.webp
---

c++ 中的优先级队列是基于堆实现的，可以快速地插入新元素，并能够快速地取出当前优先级最高（或最低）的元素。优先级队列在 <queue> 头文件中定义，是标准模板库的一部分。 使用优先级队列之前，需要包含 <queue> 头文件，并且可以自定义存储的元素类型和元素之间的比较方式。如果不指定比较方式，默认使用 std::less<T> ，元素会按照从大到小的顺序排列，最大元素优先。

## 基本使用

不指定优先级的比较方式，采用默认的 std::less<T> ，元素会按照从大到小的顺序排列，最大元素优先。

```c++
#include <iostream>
#include <queue>

int main() {
    std::priority_queue<int> pq;
    pq.push(30);
    pq.push(20);
    pq.push(50);
    pq.push(40);
    // 循环取出并打印元素，直到队列为空
    while (!pq.empty()) {
        // 访问当前优先级最高的元素
        std::cout << pq.top() << " ";
        pq.pop();
    }
    std::cout << "\n";
    return 0;
}
```
上面的代码最终会输出如下内容:

```bash
50 40 30 20
```

## 自定义比较方式 

让优先级队列按照从小到大的顺序排列，可以通过定义自己的比较函数或者使用 std::greater<T> 来实现，下面我采用 std::greater<T> 来实现。

```c++
#include <iostream>
#include <queue>
#include <functional> // for std::greater

int main() {
    // 创建一个int类型的优先级队列，元素按照从小到大的顺序排列
    std::priority_queue<int, std::vector<int>, std::greater<int>> pq;
    pq.push(30);
    pq.push(20);
    pq.push(50);
    pq.push(40);
    // 循环取出并打印元素，直到队列为空
    while (!pq.empty()) {
        std::cout << pq.top() << " ";
        pq.pop();
    }
    std::cout << "\n";
    return 0;
}
```

上面的代码最终会输出如下内容:

```bash
20 30 40 50
```

## 自定义存储类型

### 1、通过重载 < 操作符实现比较

下面是一个使用重载 < 操作符的例子，我定义了一个简单的 Person 类，根据人的年龄来决定其在优先级队列中的优先级。年龄较大的人具有较高的优先级。

```c++
#include <iostream>
#include <queue>
#include <string>

struct Person {
    std::string name;
    int age;

    Person(const std::string &n, int a)
        : name(n), age(a) {}
    // 重载 < 操作符
    bool operator<(const Person& other) const {
        // 注意：优先级队列是按照元素的 < 操作符的相反顺序排列的，
        // 因此这里使用 > 来比较，以确保年龄大的人优先级更高
        return age > other.age;
    }
};

int main() {
    std::priority_queue<Person> pq;
    pq.push(Person("Alice", 30));
    pq.push(Person("Bob", 25));
    pq.push(Person("Charlie", 35));
    pq.push(Person("Diana", 32));
    // 循环取出并打印元素，直到队列为空
    while (!pq.empty()) {
        Person p = pq.top();
        std::cout << p.name << " - " << p.age << std::endl;
        pq.pop();
    }
    std::cout << "\n";
    return 0;
}
```

上面的代码最终会输出如下内容:

```bash
Bob - 25
Alice - 30
Diana - 32
Charlie - 35
```

### 2、通过提供一个仿函数来实现

```c++
#include <iostream>
#include <queue>

struct MyObject {
    int value;
    MyObject(int v) : value(v) {}
};

class Compare {
public:
    bool operator()(const MyObject & a, const MyObject & b) {
        // 定义优先级：值较小的对象优先级更高
        return a.value > b.value;
    }
};

int main() {
    std::priority_queue<MyObject, std::vector<MyObject>, Compare> pq;
    pq.push(MyObject(30));
    pq.push(MyObject(20));
    pq.push(MyObject(50));
    pq.push(MyObject(40));
    // 循环取出并打印元素，直到队列为空
    while (!pq.empty()) {
        std::cout << pq.top().value << " ";
        pq.pop();
    }
    std::cout << "\n";
    return 0;
}
```

上面的代码最终会输出如下内容:

```bash
20 30 40 50
```


