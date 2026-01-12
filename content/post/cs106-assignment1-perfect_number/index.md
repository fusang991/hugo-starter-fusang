---
title: "Cs106 Assignment1 Perfect_number"
description: 
date: 2025-11-23T21:23:00+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
    - c++
--- 
```cpp
#include <chrono>
#include <cmath>
#include <iostream>
#include <vector>
int sum_factor(int n) {
  int sum = 0;
  for (int i = 1; i < std::sqrt(n); i++) {

    if (i == 1) {
      sum += 1;
    } else {
      if (n % i == 0) {
        sum += i + n / i;
      }
    }
  }
  return sum;
}

bool is_perfect(int n) { return (n == sum_factor(n)) && (n != 0); }

std::vector<int> find_perfect(int stop) {
  std::vector<int> perfect;

  for (int i = 0; i <= stop; i++) {
    if (is_perfect(i)) {
      perfect.push_back(i);
    }
  }
  return perfect;
}
long test_time_perfect(int stop) {
  auto begin_time = std::chrono::high_resolution_clock::now();
  find_perfect(stop);
  auto end_time = std::chrono::high_resolution_clock::now();
  auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(
      end_time - begin_time);
  long duration_time = duration.count();
  std::cout << "N=" << stop << ",time:" << duration_time << "ms\n";
  return duration_time;
}

int main(int argc, char *argv[]) {
  // std::cout << "sum_factor(1)=" << sum_factor(1) << std::endl;
  // std::cout << is_perfect(1) << std::endl;

  test_time_perfect(1000);
  test_time_perfect(10000);
  test_time_perfect(100000);
  test_time_perfect(1000000);
  std::vector<int> perfect_100000 = find_perfect(100000);
  for (const auto &i : perfect_100000) {
    std::cout << i << "\n";
  }
  // std::print("{}", perfect_100000);
  // std::cout<<perfect_100000<<"\n";
  return 0;
}
```

## 反思
这是我原本的代码，有什么问题呢？

1. 算法逻辑问题

对于循环中的完全平方数，是否存在漏算？

2. 分支预测（Branch Prediction）的代价


- 问题： 在 sum_factor 的循环中，if (i == 1) 这行代码在每次循环都会执行，但只有第一次是真的。
- 深度思考： 现代 CPU 依赖流水线和分支预测。这种极其倾斜的分支（99.9% 为 false）虽然 CPU 能预测对，但仍有指令开销。如果把 1 的处理提到循环外面，循环从 2 开始，对性能会有微小的提升吗？更深层地，if (n % i == 0) 这种无法预测的取模分支，是算法性能的最大杀手，有没有办法消除它？

3. std的隐形开销和类型转换

- 编译器是否足够聪明，把sqrt移到循环外面？
- sqrt对应的浮点数运算慢，能否用i*i <=n代替sqrt运算

4. 内存分配策略：std::vector 的扩容机制

- vector的内存结构是怎么样的，算法中是先分配空间再填入还是挨个push back.vector的扩容倍数一般是多少

5. 类型系统的安全性：int 够用吗？

- cpp中溢出会报错吗？函数返回类型是选择auto还是long，int这些显示类型。无符号和有符号类型有哪些，有哪些区别




软件工程思考(gemini提供的思路)
- 
8. 接口设计的通用性：高阶函数与 Lambda
问题： test_time_perfect 目前不仅负责计时，还负责打印，甚至硬编码了调用 find_perfect。如果我想测试 sum_factor 函数的耗时呢？甚至测试另一个完全不同的排序算法的耗时呢？你需要复制粘贴代码吗？
深度思考： 如何修改 test_time_perfect，使其接受一个**函数对象（Function Object）**或 Lambda 表达式作为参数？这就涉及到将“计时逻辑”与“业务逻辑”解耦，是函数式编程在 C++ 中的体现。
9. 并行计算（Concurrency）：榨干多核 CPU
问题： 你的 find_perfect 是单线程的，依次检查 0 到 stop。现在的 CPU 都是多核的。检查 i=100 和检查 i=200 是否完美是完全独立的。
深度思考： 这是一个“尴尬并行（Embarrassingly Parallel）”的问题。如何利用 std::async、std::thread 甚至 C++17 的 std::execution::par 来并行化这个循环？并行化后的性能瓶颈会从 CPU 计算转移到内存写入（vector 的线程安全写入）上吗？
10. 现代 C++ 的表达力：Ranges 与 View
问题： 代码中 find_perfect 先计算出所有结果存入 vector，再由 main 打印。如果 stop 是 10亿，vector 会占用巨大内存，尽管我们只需要打印出来看看。
深度思考： 是否可以设计一个“生成器（Generator）”或使用 C++20 的 std::ranges？这种模式下，程序是“惰性（Lazy）”的：你需要打印下一个，我才去计算下一个。这种**管道（Pipeline）**式的抽象如何改变数据的流动方式？
