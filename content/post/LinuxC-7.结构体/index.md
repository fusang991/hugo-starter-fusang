---
title: "LinuxC 7.结构体"
description: 
date: 2025-11-19T15:17:54+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories: 
    - linuxC
---

## q1:利用结构体实现分数的抽象
```c
// abstraction of rational
//

#include "math.h"
#include "stdint.h"
#include "stdio.h"
#include "stdlib.h"

typedef struct {
  int numerator;   // 分子
  int denominator; // 分母
} rational;

rational make_rational(int a, int b) {
  if ((a * b < 0)) {
    a = -abs(a);
    b = abs(b);
  } else {
    a = abs(a);
    b = abs(b);
  }
  rational r = {a, b};

  return r;
}
int gcd(int a, int b) {
  a = abs(a);
  b = abs(b);
  int max_ab = a > b ? a : b;
  int min_ab = a > b ? b : a;
  if (min_ab == 0) {
    return max_ab;
  } else {
    return gcd(min_ab, max_ab % min_ab);
  }
}

rational reduction(rational r) {
  int a = r.numerator;
  int b = r.denominator;
  int gcd_ab = gcd(a, b);
  rational k = {a / gcd_ab, b / gcd_ab};
  return k;
}

void printf_rational(rational r) {
  if (r.denominator == 0) {
    printf("infinity\n");
  } else if (r.denominator == 1) {
    printf("%d\n", r.numerator);
  } else {
    printf("%d/%d\n", r.numerator, r.denominator);
  }
}

rational mux_rational(rational r1, rational r2) {
  int a = r1.numerator * r2.denominator + r2.numerator * r1.denominator;
  int b = r1.denominator * r2.denominator;
  rational r = {a, b};
  return reduction(r);
}

rational sub_rational(rational r1, rational r2) {
  int a = r1.numerator * r2.denominator - r2.numerator * r1.denominator;
  int b = r1.denominator * r2.denominator;
  rational r = {a, b};
  return reduction(r);
}

rational mul_rational(rational r1, rational r2) {
  int a = r1.numerator * r2.numerator;
  int b = r1.denominator * r2.denominator;
  rational r = {a, b};
  return reduction(r);
}

rational div_rational(rational r1, rational r2) {
  int a = r1.numerator * r2.denominator;
  int b = r1.denominator * r2.numerator;
  rational r = {a, b};
  return reduction(r);
}

int main(int argc, char *argv[]) {
  rational r1 = make_rational(1, -8);
  rational r2 = make_rational(1, 8);
  printf_rational(mux_rational(r1, r2));
  printf_rational(sub_rational(r1, r2));
  printf_rational(mul_rational(r1, r2));
  printf_rational(div_rational(r1, r2));
  return EXIT_SUCCESS;
}
```

## q2: 预测一下两段代码结果，思考enum与宏定义的差异

```c

//code1
#include <stdio.h>
enum coordinate_type { RECTANGULAR = 1, POLAR };
int main(void) {
  int RECTANGULAR;
  printf("%d %d\n", RECTANGULAR, POLAR);
  return 0;
}

//输出： 垃圾值，2
//解释：enum在全局定义，会以局部变量定义

//code2 
#include <stdio.h>
int main(void) {
  enum coordinate_type { RECTANGULAR = 1, POLAR };
  // int RECTANGULAR;
  printf("%d %d\n", RECTANGULAR, POLAR);
  return 0;
}

//结果：报错
//解释:命名冲突
```

 - 枚举：在编译阶段处理，有定义域，枚举值存在于编译器的符号表里，编译后直接变成了机器指令里的数字，不占用数据内存空间。
- 宏：在预处理阶段处理，直接替换