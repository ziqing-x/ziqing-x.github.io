---
title: 封装 spdlog
tags: [log]
category: c++
image:
    path: /assets/img/headers/spdlog.webp
---

spdlog 是一个快速、可扩展的 c++ 日志库，它提供了简单易用的接口和灵活的配置选项。spdlog 支持多种日志级别、多线程安全，可以将日志输出到终端、文件或者其他自定义的目标。它具有高性能和低开销的特点，适用于各种规模的应用程序和系统。

## 获取源码

如果要将源码添加进你的工程里，请从 github 获取

```bash
https://github.com/gabime/spdlog
```
+ 如果使用的是 conan 包管理，可以从这里获取

```bash
https://conan.io/center/recipes/spdlog
```

## 封装 spdlog

```c++
// 单例模板
#include <iostream>
#include <string>

template<class T>
class Singleton
{
protected:
    Singleton(const Singleton &) = delete;
    Singleton &operator=(const Singleton &) = delete;
    Singleton() = default;
    ~Singleton() = default;

public:
    template<typename... Args>
    static T &instance(Args &&...args)
    {
        static T obj(std::forward<Args>(args)...);
        return obj;
    }
};



// spdlog wraper
#include "spdlog/async.h"
#include "spdlog/sinks/basic_file_sink.h"
#include "spdlog/sinks/rotating_file_sink.h"
#include "spdlog/sinks/stdout_color_sinks.h"
#include "spdlog/spdlog.h"

#include <memory>

#define LOG_TOPIC "app"                               // 日志tag
#define LOG_FILE_NAME "./log/" LOG_TOPIC ".log"       // 日志文件名
#define LOG_FILE_SIZE 1024 * 1024 * 3                 // 3MB
#define LOG_ROTATION 3                                // 日志文件满3个时开始滚动日志
#define PATTERN  "[%Y-%m-%d %H:%M:%S.%f] [%^%L%$] %v" // 日志格式
#define LOG_FLUSH_ON spdlog::level::info              // 当打印这个级别日志时flush

#define LOGE(...) Singleton<Logger>::Instance().log_error(__VA_ARGS__)
#define LOGW(...) Singleton<Logger>::Instance().log_warn(__VA_ARGS__)
#define LOGI(...) Singleton<Logger>::Instance().log_info(__VA_ARGS__)
#define LOGD(...) Singleton<Logger>::Instance().log_debug(__VA_ARGS__)
#define LOGC(...) Singleton<Logger>::Instance().log_critical(__VA_ARGS__)

class Logger
{
private:
    std::unique_ptr<spdlog::logger> logger_;

public:
    Logger()
    {
        auto function = [&]() {
            auto stdout_sink = std::make_shared<spdlog::sinks::stdout_color_sink_mt>();
            auto rotating_sink = std::make_shared<spdlog::sinks::rotating_file_sink_mt>(LOG_FILE_NAME, LOG_FILE_SIZE, LOG_ROTATION);
            std::vector<spdlog::sink_ptr> sinks{stdout_sink, rotating_sink};
            logger_ = std::make_unique<spdlog::logger>(LOG_TOPIC, sinks.begin(), sinks.end());
            logger_->set_pattern(PATTERN);
            if (is_debug_mode())
            {
                logger_->set_level(spdlog::level::debug);
            }
            else
            {
                logger_->set_level(spdlog::level::info);
            }
            logger_->flush_on(LOG_FLUSH_ON);
        };
        try
        {
            function();
        } catch (...)
        {
            std::cout << "init failed" << std::endl;
        }
    }

    template<typename... Args>
    inline void log_error(const char *fmt, Args... args)
    {
        logger_->error(fmt, args...);
    }

    template<typename... Args>
    inline void log_warn(const char *fmt, Args... args)
    {
        logger_->warn(fmt, args...);
    }

    template<typename... Args>
    inline void log_info(const char *fmt, Args... args)
    {
        logger_->info(fmt, args...);
    }

    template<typename... Args>
    inline void log_debug(const char *fmt, Args... args)
    {
        logger_->debug(fmt, args...);
    }

    template<typename... Args>
    inline void log_critical(const char *fmt, Args... args)
    {
        logger_->critical(fmt, args...);
    }

private:
    bool is_debug_mode()
    {
        char *var = getenv("debug");
        if (nullptr == var)
        {
            return false;
        }
        if (0 == strcmp(var, "on"))
        {
            return true;
        }
        return false;
    }
};
```
