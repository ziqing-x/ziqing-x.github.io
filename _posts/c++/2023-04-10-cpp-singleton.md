---
title: 单例模式
tags: [设计模式]
category: c++
---

## 一、简介
+ 单例模式分为懒汉式和饿汉式:
  + 饿汉式，实例在进入 main 函数之前就已经存在了;
  + 懒汉式，第一次获取实例的时候才分配内存;
+ 设计思路:
  + 构造函数私有化；
  + 提供一个全局的静态方法。

## 二、例子
+ 懒汉式单例

```c++
#include <string>

template<class T>
class Singleton
{
public:
    template<typename... Args>
    static T &instance(Args &&...args)
    {
        static T Singleton(std::forward<Args>(args)...);
        return instance;
    }

    Singleton(const Singleton &) = delete;
    Singleton &operator=(const Singleton &) = delete;

protected:
    Singleton() = default;
    ~Singleton() = default;
};

////////////////////////////////////////////////////////////////////
// Usage
#include <iostream>

class MyClass
{
public:
    void say_hello()
    {
        std::cout << "Hello" << std::endl;
    }
}

int main()
{
    auto &my_class = Singleton<MyClass>::instance();
    my_class.say_hello();

    // 也可以使用 MyClass 直接继承 Singleton 来使用
    // auto my_class = MyClass::instance();
    // my_class.say_hello();
}
```

+ 饿汉式单例

```c++
#include <iostream>
#include <memory>
#include <mutex>
#include <string>

template<class T>
class Singleton
{
public:
    template<typename... Args>
    static std::unique_ptr<T> &instance(Args &&...args)
    {
        std::call_once(flag_, [&]() {
            instance_ = std::make_unique<T>(std::forward<Args>(args)...);
            }
        );
        return instance_;
    }

    Singleton(const Singleton &) = delete;
    Singleton &operator=(const Singleton &) = delete;

protected:
    Singleton() = default;
    ~Singleton() = default;

protected:
    static std::unique_ptr<T> instance_;
    static std::once_flag flag_; // 保证多线程调用时只make_unique一次
};

template<class T>
std::unique_ptr<T> Singleton<T>::instance_ = nullptr;
template<class T>
std::once_flag Singleton<T>::flag_;

////////////////////////////////////////////////////////////////////
// Usage
class Person : public Singleton<Person>
{
public:
    Person(const std::string &name, int age)
    {
        name_ = name;
        age_ = age;
    }

    void show_info()
    {
        std::cout << "姓名: " << name_ << std::endl;
        std::cout << "年龄: " << age_ << std::endl;
    }

private:
    std::string name_;
    int age_;
};

int main()
{
    Person::instance("xxx", 15)->show_info();
    return 0;
}
```