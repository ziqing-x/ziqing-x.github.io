---
title: 单例模式
tags: [设计模式]
category: c++
image:
    path: /assets/img/headers/singleton.webp
---

单例模式是一种设计模式，用于确保一个类只有一个实例，并提供一个全局访问点。在 c++ 中，可以通过使用静态成员变量和静态成员函数来实现单例模式。通过将构造函数设置为私有，限制了类的实例化，然后通过静态成员函数返回类的唯一实例，确保在整个程序中只有一个实例被创建和访问。这种模式在需要全局共享的资源、配置信息或对象管理等场景下非常有用。单例模式分为懒汉式和饿汉式:
+ 饿汉式，实例在进入 main 函数之前就已经存在了;
+ 懒汉式，第一次获取实例的时候才分配内存;

## 懒汉式单例

```c++
#include <string>

template<class T>
class Singleton
{
protected:
    Singleton() = default;
    ~Singleton() = default;

public:

    Singleton(const Singleton &) = delete;
    Singleton &operator=(const Singleton &) = delete;

    template<typename... Args>
    static T &instance(Args &&...args)
    {
        static T Singleton(std::forward<Args>(args)...);
        return instance;
    }
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

## 饿汉式单例

```c++
#include <iostream>
#include <memory>
#include <mutex>
#include <string>

template<class T>
class Singleton
{
protected:
    static std::unique_ptr<T> instance_;
    static std::once_flag flag_; // 保证多线程调用时只make_unique一次

protected:
    Singleton() = default;
    ~Singleton() = default;

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