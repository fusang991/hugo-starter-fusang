---
title: "Cs106 Assignment1 Perfect_number"
description: 
date: 2025-11-23T21:23:00+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft:false
categories:
    - cs106B
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