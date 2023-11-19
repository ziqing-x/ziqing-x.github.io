---
title: 自己实现一个 any
tags: [any]
category: c++
image:
    path: /assets/img/headers/my_any.webp
---

最近的业务中需要用到 std::any，但编译器版本又太低，于是自己实现了一个(参考 [llvm](https://github.com/llvm-mirror/llvm/blob/master/include/llvm/ADT/Any.h))，用法参见std::any的用法，前面有介绍过。

## 代码实现

```c++
#include "make_unique.h"
#include <iostream>
#include <memory>
#include <typeindex>

class Any
{
private:
    struct HoldBase
    {
        virtual ~HoldBase() = default;
        virtual std::unique_ptr<HoldBase> clone() const = 0;
    };

    template<typename T>
    struct Hold : public HoldBase
    {
        Hold(const T &value)
            : value_(value)
        {}
        Hold(T &&value)
            : value_(std::move(value))
        {}
        inline std::unique_ptr<HoldBase> clone() const override
        {
            return std::make_unique<Hold<T>>(value_);
        }
        T value_;

    private:
        Hold &operator=(const Hold &other) = delete;
        Hold(const Hold &other) = delete;
    };

public:
    Any(void)
        : type_index_(typeid(void))
    {}
    Any(const Any &other)
        : holdbase_(other.holdbase_ ? other.holdbase_->clone() : nullptr)
        , type_index_(other.type_index_)
    {}
    Any(Any &&other)
        : holdbase_(std::move(other.holdbase_))
        , type_index_(other.type_index_)
    {}
    template<typename T, class = typename std::enable_if<!std::is_same<typename std::decay<T>::type, Any>::value, T>::type>
    Any(T &&value)
        : holdbase_(new Hold<typename std::decay<T>::type>(std::forward<T>(value)))
        , type_index_(typeid(typename std::decay<T>::type))
    {}
    Any &operator=(Any &&other)
    {
        holdbase_ = std::move(other.holdbase_);
        type_index_ = other.type_index_;
        return *this;
    }
    Any &operator=(const Any &other)
    {
        if (other.holdbase_)
        {
            holdbase_ = other.holdbase_->clone();
            type_index_ = other.type_index_;
        }
        return *this;
    }
    Any &swap(Any &other)
    {
        std::swap(holdbase_, other.holdbase_);
        std::swap(type_index_, other.type_index_);
        return *this;
    }

    inline bool has_value() const
    {
        return !!holdbase_;
    }

    inline void reset()
    {
        holdbase_.reset();
        type_index_ = typeid(void);
    }

private:
    template<class T>
    inline bool Is() const
    {
        return type_index_ == typeid(T);
    }
    template<class T>
    inline T &any_cast()
    {
        if (!Is<T>())
        {
            if (type_index_ != typeid(void))
            {
                std::cout << "can not cast " << type_index_.name() << " to " << typeid(T).name() << std::endl;
            }
            throw std::bad_cast();
        }
        auto hold = dynamic_cast<Hold<T> *>(holdbase_.get());
        return hold->value_;
    }

private:
    template<class T>
    friend T &any_cast(Any &Value);
    template<class T>
    friend T &any_cast(Any &&Value);
    template<class T>
    friend bool is_type(Any &value);
    template<class T>
    friend bool is_type(Any &&value);
    std::unique_ptr<HoldBase> holdbase_;
    std::type_index type_index_;
};

template<class T>
T &any_cast(Any &value)
{
    return value.any_cast<T>();
}
template<class T>
T &any_cast(Any &&value)
{
    return value.any_cast<T>();
}
template<class T>
bool is_type(Any &&value)
{
    return value.Is<T>();
}
template<class T>
bool is_type(Any &value)
{
    return value.Is<T>();
}

// Usage
int main()
{
    int num = 7;
    Any a = num;
    int ret = any_cast<int>(a);
    std::cout << ret << std::endl;

    return 0;
}
```