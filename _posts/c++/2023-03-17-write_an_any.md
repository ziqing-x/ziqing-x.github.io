---
title: 自己实现一个any
tags: [any]
category: c++
date: 2023-03-17 16:00:00 +0800
---

最进工作中需要在arm架构设备上实现一个告警数据中心，代码实现过程中需要用到std::any，可是std::any是c++17才加入标准库的，只好自己实现一个(参考[llvm](https://github.com/llvm-mirror/llvm/blob/master/include/llvm/ADT/Any.h)里的设计思想)，话不多说，上代码：

Any.h

```c++
#ifndef __ANY_H__
#define __ANY_H__

#include <iostream>
#include <memory>
#include <typeindex>

namespace My {
class Any {
  private:
    struct HoldBase {
        virtual ~HoldBase() = default;
        virtual std::unique_ptr<HoldBase> Clone() const = 0;
    };

    template <typename T>
    struct Hold : public HoldBase {
        Hold(const T &value) : value_(value) {}
        Hold(T &&value) : value_(std::move(value)) {}
        inline std::unique_ptr<HoldBase> Clone() const override {
            return std::make_unique<Hold<T>>(value_);
        }
        T value_;

      private:
        Hold &operator=(const Hold &other) = delete;
        Hold(const Hold &other) = delete;
    };

  public:
    Any(void) : type_index_(typeid(void)) {}
    Any(const Any &other)
        : holdbase_(other.holdbase_ ? other.holdbase_->Clone() : nullptr),
          type_index_(other.type_index_) {}
    Any(Any &&other) : holdbase_(std::move(other.holdbase_)), type_index_(other.type_index_) {}
    template <typename T, class = typename std::enable_if<
                              !std::is_same<typename std::decay<T>::type, Any>::value, T>::type>
    Any(T &&value)
        : holdbase_(new Hold<typename std::decay<T>::type>(std::forward<T>(value))),
          type_index_(typeid(typename std::decay<T>::type)) {}
    Any &operator=(Any &&other) {
        holdbase_ = std::move(other.holdbase_);
        type_index_ = other.type_index_;
        return *this;
    }
    Any &operator=(const Any &other) {
        if (other.holdbase_) {
            holdbase_ = other.holdbase_->Clone();
            type_index_ = other.type_index_;
        }
        return *this;
    }
    Any &swap(Any &other) {
        std::swap(holdbase_, other.holdbase_);
        std::swap(type_index_, other.type_index_);
        return *this;
    }
    inline bool HasValue() const { return !!holdbase_; }
    inline void Reset() {
        holdbase_.reset();
        type_index_ = typeid(void);
    }

  private:
    template <class T>
    inline bool Is() const {
        return type_index_ == typeid(T);
    }
    template <class T>
    inline T &AnyCast() {
        if (!Is<T>()) {
            if (type_index_ != typeid(void)) {
                std::cout << "can not cast " << type_index_.name() << " to " << typeid(T).name()
                          << std::endl;
            }
            throw std::bad_cast();
        }
        auto hold = dynamic_cast<Hold<T> *>(holdbase_.get());
        return hold->value_;
    }

  private:
    template <class T>
    friend T &any_cast(Any &Value);
    template <class T>
    friend T &any_cast(Any &&Value);
    template <class T>
    friend bool is_type(Any &value);
    template <class T>
    friend bool is_type(Any &&value);
    std::unique_ptr<HoldBase> holdbase_;
    std::type_index type_index_;
};

template <class T>
T &any_cast(Any &value) {
    return value.AnyCast<T>();
}
template <class T>
T &any_cast(Any &&value) {
    return value.AnyCast<T>();
}
template <class T>
bool is_type(Any &&value) {
    return value.Is<T>();
}
template <class T>
bool is_type(Any &value) {
    return value.Is<T>();
}
} // namespace My
#endif // __ANY_H__
```

main.cpp

```c++
#include "Any.h"
#include <string>
#include <iostream>
#include <tuple>

struct Info {
    std::string who;
    std::string where;
    int age;
};

int main() {
    // 1
    My::Any str = std::string("hello, std::string");
    std::cout << My::any_cast<std::string>(str) << "\n";

    // 2
    My::Any cstr = "hello, const char*";
    std::cout << My::any_cast<const char *>(cstr) << "\n";

    // 3
    My::Any inter = 8;
    std::cout << My::any_cast<int>(inter) << "\n";

    // 4
    My::Any db = 8.8;
    std::cout << My::any_cast<double>(db) << "\n";

    // 5
    float flt = 98.8;
    My::Any f(flt);
    std::cout << My::any_cast<float>(f) << "\n";

    // 6
    auto person = std::make_tuple<std::string, int>("aurhon", 28);
    My::Any pers(person);
    auto p = My::any_cast<std::tuple<std::string, int>>(pers);
    std::cout << std::get<0>(p) << " " << std::get<1>(p) << "\n";

    // 7
    Info info = {"小王", "北京", 25};
    My::Any inf(info);
    auto i = My::any_cast<Info>(inf);
    std::cout << i.who << " " << i.where << " " << i.age << "\n";

    //8
    My::Any test(888);
    My::Any t(test);
    std::cout << My::any_cast<int>(t) << "\n";

    //9
    My::Any test2(999);
    My::Any t2 = test2;
    std::cout << My::any_cast<int>(t2) << "\n";

    return 0;
}
```
