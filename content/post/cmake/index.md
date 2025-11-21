---
title: "Cmake"
description: 
date: 2025-11-21T21:47:58+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
---
这是一份为你定制的 **CMake 极简实战指南**。

针对你的背景（C 基础 + LazyVim + WSL），我剔除了那些复杂的、过时的写法，只保留**现代 C++ (Modern CMake)** 的核心用法。

---

### 1. 核心概念：它是个“生成器”

CMake 不会编译代码，它只是**指挥**别的工具去编译。
*   **输入**：`CMakeLists.txt`（你写的脚本）
*   **动作**：`cmake` 命令
*   **输出**：Makefile（或者 Ninja 文件）

你只需要维护 `CMakeLists.txt`，然后让 CMake 帮你生成那个让你头疼的 Makefile。

---

### 2. 最小可行配置 (Boilerplate)

在你的项目根目录下创建一个名为 `CMakeLists.txt` 的文件（文件名大小写敏感，必须完全一致）。

#### 场景 A：只有几个源文件（作业常用）

```cmake
# 1. 指定 CMake 最低版本 (3.10 是一个安全的基准)
cmake_minimum_required(VERSION 3.10)

# 2. 定义项目名称
project(CS106B_Assignment1)

# 3. 设置 C++ 标准 (C++17 或 C++20)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON) # 强制要求，不支持就报错

# 4. 开启常用警告 (类似 gcc -Wall -Wextra)，C 程序员的好习惯
add_compile_options(-Wall -Wextra)

# 5. 生成 compile_commands.json (给 LazyVim/Clangd 用的，极其重要！)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# 6. 定义要生成的可执行文件
# 语法：add_executable(程序名 源文件1 源文件2 ...)
add_executable(main main.cpp utils.cpp logic.cpp)
```

---

### 3. 进阶场景：源文件很多，分目录管理

如果你想像真正的 C++ 项目那样组织文件：
```text
Project/
├── CMakeLists.txt
├── src/
│   ├── main.cpp
│   └── helper.cpp
└── include/
    └── helper.h
```

对应的 `CMakeLists.txt` 写法：

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# 自动查找 src 目录下所有的 .cpp 文件，存入变量 SOURCES 中
# 注意：如果添加新文件，需要重新运行 cmake
file(GLOB SOURCES "src/*.cpp")

# 告诉编译器去哪里找 .h 头文件
include_directories(include)

# 生成可执行文件，使用上面找到的 SOURCES 列表
add_executable(app ${SOURCES})
```

---

### 4. 标准构建流程 (CLI)

在 WSL 终端中，永远遵循 **"Out-of-source Build"**（外部构建）原则。不要把生成的临时文件和你的源码混在一起。

**步骤 1：配置 (Configure)**
告诉 CMake 在 `build` 文件夹下生成构建文件。
```bash
cmake -S . -B build
```
*   `-S .` : Source 在当前目录。
*   `-B build` : Build artifacts 放在 `build` 目录（如果没有会自动创建）。

**步骤 2：构建 (Build)**
这一步相当于执行了 `make`。
```bash
cmake --build build
```
*   如果你想利用多核 CPU 加速编译（比如 8 核）：
    `cmake --build build -- -j8`

**步骤 3：运行**
```bash
./build/main
# 或者
./build/app
```

---

### 5. 给 LazyVim 用户的特别提示

你在 `CMakeLists.txt` 里加了 `set(CMAKE_EXPORT_COMPILE_COMMANDS ON)` 后，CMake 会在 `build` 目录下生成一个 `compile_commands.json`。

**这是 LSP (Clangd) 的“圣经”。**
有了它，Clangd 才知道你的项目包含了哪些头文件路径、用了什么标准。

**为了让 LazyVim 自动读取它：**
通常 LazyVim 足够智能可以去 build 目录找。如果它找不到（表现为头文件报错 `file not found`），你可以做一个软链接到根目录：

```bash
ln -s build/compile_commands.json .
```

---

### 6. 常用语法速查表

*   **变量**：
    ```cmake
    set(MY_VAR "Hello")
    message(${MY_VAR})  # 打印变量
    ```
*   **列表**（其实就是用空格或分号隔开的字符串）：
    ```cmake
    set(MY_FILES a.cpp b.cpp c.cpp)
    ```
*   **条件判断**：
    ```cmake
    if(WIN32)
        # Windows 专用配置
    elseif(UNIX)
        # Linux/Mac 配置
    endif()
    ```
*   **注释**：使用 `#`。

### 总结

对于你现在的阶段，记住这**三板斧**就够了：
1.  **`cmake_minimum_required`** & **`project`** (开头仪式)
2.  **`set(CMAKE_CXX_STANDARD 17)`** (现代化)
3.  **`add_executable(main file1.cpp file2.cpp)`** (干正事)

遇到新文件，就往 `add_executable` 后面加；遇到头文件找不到，就加 `include_directories`。就这么简单。