---
title: "Cs106 Assignment1 Soundex"
description: 
date: 2025-12-03T22:49:04+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
---

## 代码
```cpp
#include <cinttypes>
#include <cstdio>
#include <iostream>
#include <string>
#include <string_view>
#include <vector>

using std::cout;
using std::endl;
using std::string;
using std::string_view;
using std::vector;

constexpr string_view letter0 = "AEIOUHWYaeiouhwy";
constexpr string_view letter1 = "BFPVbfpv";
constexpr string_view letter2 = "CGJKQSXZcgjkqsxz";
constexpr string_view letter3 = "DTdt";
constexpr string_view letter4 = "Ll";
constexpr string_view letter5 = "MNmn";
constexpr string_view letter6 = "Rr";
string letter_to_digit(string number, char c) {
  if (letter0.contains(c)) {
    number.push_back('0');
    return number;
  } else if (letter2.contains(c)) {
    number.push_back('2');
    return number;
  } else if (letter3.contains(c)) {
    number.push_back('3');
    return number;
  } else if (letter4.contains(c)) {
    number.push_back('4');
    return number;
  } else if (letter5.contains(c)) {
    number.push_back('5');
    return number;
  } else if (letter6.contains(c)) {
    number.push_back('6');
    return number;
  } else {
    return number;
  }
}
string name_to_number(string name) {
  string number;
  for (char c : name) {
    number = letter_to_digit(number, c);
  }
  return number;
}

string coalesce(string number) {
  string simplified_number;
  for (size_t i = 0; i < number.size(); i++) {
    if (i == 0) {
      simplified_number.push_back(number[i]);
    } else {
      if (number[i] == number[i - 1]) {
      } else {
        simplified_number.push_back(number[i]);
      }
    }
  }
  return simplified_number;
}

string replace_first_digit(string number, char first_letter) {
  number[0] = first_letter;
  return number;
}

string discard0(string letter_number) {
  string discard0_letter_number;
  for (size_t i = 0; i < letter_number.size(); i++) {
    if (letter_number[i] == '0') {
    } else {
      discard0_letter_number.push_back(letter_number[i]);
    }
  }
  return discard0_letter_number;
}

string make_length4(string letter_number) {
  int size = letter_number.size();
  if (size == 4) {
  } else if (size > 4) {
    letter_number.erase(4);
  } else if (size < 4) {
    int n = 4 - size;
    letter_number.append(n, '0');
  }
  return letter_number;
}
string soundex(string name) {
  string number = name_to_number(name);
  number = coalesce(number);
  number = replace_first_digit(number, name[0]);
  number = discard0(number);
  number = make_length4(number);
  return number;
}
int main(int argc, char *argv[]) {
  // string number;
  // string number1;
  // number = letter_to_digit(number, 'A');
  // number.push_back('a');
  //
  // number1 = name_to_number("AfaILODR'M[");
  // cout << number1 << endl;
  // cout << number << "\n";
  // string number2 = coalesce("222025");
  // cout << number2 << endl;
  // cout << discard0("200233") << endl;
  string name1 = "Curie";
  string number1 = soundex(name1);
  string name2 = "O’Conner";
  string number2 = soundex(name2);
  cout << number1 << endl;
  cout << number2 << endl;
}
```

## 反思


1. 在c++17中引入了string view，string和string_view可以理解为变量和指针的关系。在上面代码中，可以用string_view作为函数参数，这样就不用每次调用函数都拷贝一遍字符串了。

2. 在string需要修改时，不能传入string_view,