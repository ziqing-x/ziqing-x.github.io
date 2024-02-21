---
title: cmake IMPORTED 用法
tags: [cmake]
category: c++
image:
    path: /assets/img/headers/cmake.webp
---

在 cmake 中, IMPORTED 目标是指那些在项目外部预先构建好的库或可执行文件。通过将这些库或可执行文件作为导入的目标引入，可以在项目中使用它们，就像使用项目内构建的目标一样。这样做的好处是可以方便地重用已有的二进制文件，而无需从源代码重新构建它们，这对于依赖于第三方库的项目尤其有用。

## 举例

目录结构如下

```bash
├── your_lib
│   ├── include
│   │   └── your_lib.h
│   ├── lib
│   │   └── libyour_lib.a
│   └── CMakeLists.txt
├── CMakeLists.txt
└── main.cpp
```

其中 your_lib 下的 CMakeLists.txt 内容如下

```bash
project(your_lib)
add_library(${PROJECT_NAME} STATIC IMPORTED GLOBAL)
set_target_properties(
    ${PROJECT_NAME} PROPERTIES
    IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/lib/libyour_lib.a
    INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_CURRENT_SOURCE_DIR}/include
)
```

顶层目录的 CMakeLists.txt 内容如下

```bash
cmake_minimum_required(VERSION 3.10)
project(main)

add_executable(main main.cpp)
target_link_libraries(main your_lib)
add_subdirectory(your_lib)
```



