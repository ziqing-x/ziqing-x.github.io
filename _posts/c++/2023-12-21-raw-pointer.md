---
title: 如何防止裸指针内存泄漏
tags: [指针]
category: c++
image:
    path: /assets/img/headers/point.webp
---

在 C 语言的世界里，裸指针是可以随意使用的，但是在 C++ 中，裸指针是不能随意使用的，因为它有可能导致内存泄漏。但是有时候确实需要绕不开裸指针，该怎么办？下面介绍一种使用 c++ 的方式来优雅的管理裸指针的方法。

## 示例

```c++
#include <memory>

int main()
{
    char *p = nullptr;
    std::shared_ptr<char>(p, [](char *p) {
        delete[] p;
    });

    return 0;
}
```

## 说明

在上面的示例中，，我们使用了 `std::shared_ptr` 来管理裸指针，当 `std::shared_ptr` 的引用计数为 0 时，会自动调用 `delete[]` 来释放内存，这样就避免了内存泄漏。