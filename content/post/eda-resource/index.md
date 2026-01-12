---
title: "Eda Resource"
description: 
date: 2026-01-12T23:27:09+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
---
下面是我筛过的 **EDA 学习教材 / notes / slides（都带网址）**，尽量选“短、讲得透、能直接看”的。

---

## 物理设计 / VLSI CAD 总览（强烈推荐先看）

**Georgia Tech ECE6133（Physical Design Automation）— slides 很全、很清晰**

```text
https://limsk.ece.gatech.edu/course/ece6133/slides/intro.pdf
```

```text
https://limsk.ece.gatech.edu/course/ece6133/slides/placement.pdf
```

```text
https://limsk.ece.gatech.edu/course/ece6133/slides/steiner.pdf
```

([Limsk][1])

**Northwestern ECE357（Intro to VLSI CAD）— lecture notes 风格，覆盖 partition/placement/routing/compaction**

```text
https://www.eecs.northwestern.edu/~haizhou/357/
```

（例：lec1）

```text
https://www.eecs.northwestern.edu/~haizhou/357/lec1.pdf
```

([西北大学电气与计算机工程][2])

**MIT 6.375（复杂数字系统，含“synthesis/placement/routing”速览）**

```text
https://csg.csail.mit.edu/6.375/6_375_2006_www/handouts/lectures/L05-Synthesis-Placement-Routing.pdf
```

（课程资源页）

```text
https://csg.csail.mit.edu/6.375/6_375_2009_www/resources.html
```

([计算结构组][3])

**NTU（Y.-W. Chang）VLSI Design Automation 讲义（偏“短而密”）**

```text
https://cc.ee.ntu.edu.tw/~ywchang/Courses/EDA04/lec1.pdf
```

（Routing 讲义也很值得扫一遍）

```text
https://cc.ee.ntu.edu.tw/~ywchang/Courses/PD_Source/EDA_routing.pdf
```

([东南大学][4])

**NPTEL（VLSI Physical Design with Timing Analysis）— 系统课，单节视频/讲义都很短**

```text
https://nptel.ac.in/courses/108107380
```

([NPTEL][5])

---

## 你关心的 Placement（再给你一个“从课件到可复现”的闭环）

**OpenROAD：Global Placement 文档（基于 RePlAce）**

```text
https://openroad.readthedocs.io/en/latest/main/src/gpl/README.html
```

([OpenROAD][6])

**RePlAce（源码+说明）**

```text
https://github.com/The-OpenROAD-Project/RePlAce
```

([GitHub][7])

**OpenROAD 教程资源汇总（含 slides/仓库/工具链）**

```text
https://the-openroad-project.github.io/micro2022tutorial/content/4-resources.html
```

([开放之路项目][8])

---

## “能动手”的开源工具文档（建议边学边跑）

**Yosys（综合）**

```text
https://yosyshq.readthedocs.io/projects/yosys/en/latest/
```

([YosysHQ Docs][9])

**OpenSTA（静态时序分析）**

```text
https://github.com/The-OpenROAD-Project/OpenSTA
```

([GitHub][10])

**KLayout（版图查看/DRC/LVS）**

```text
https://www.klayout.de/doc/manual/index.html
```

([klayout.de][11])

[1]: https://limsk.ece.gatech.edu/course/ece6133/slides/intro.pdf?utm_source=chatgpt.com "Introduction - Sung Kyu Lim - Georgia Institute of Technology"
[2]: https://www.eecs.northwestern.edu/~haizhou/357/?utm_source=chatgpt.com "EECS 357 Introduction to VLSI CAD"
[3]: https://csg.csail.mit.edu/6.375/6_375_2006_www/handouts/lectures/L05-Synthesis-Placement-Routing.pdf?utm_source=chatgpt.com "Logic Synthesis, Placement and Routing 6.375 Lecture 5"
[4]: https://cc.ee.ntu.edu.tw/~ywchang/Courses/EDA04/lec1.pdf?utm_source=chatgpt.com "VLSI Design Automation"
[5]: https://nptel.ac.in/courses/108107380?utm_source=chatgpt.com "Course Details"
[6]: https://openroad.readthedocs.io/en/latest/main/src/gpl/README.html?utm_source=chatgpt.com "Global Placement - OpenROAD documentation - Read the Docs"
[7]: https://github.com/The-OpenROAD-Project/RePlAce?utm_source=chatgpt.com "The-OpenROAD-Project/RePlAce"
[8]: https://the-openroad-project.github.io/micro2022tutorial/content/4-resources.html?utm_source=chatgpt.com "Reference & Resources | OpenROAD tutorial at MICRO 2022"
[9]: https://yosyshq.readthedocs.io/projects/yosys/en/latest/?utm_source=chatgpt.com "YosysHQ Yosys 0.60-dev documentation"
[10]: https://github.com/The-OpenROAD-Project/OpenSTA?utm_source=chatgpt.com "The-OpenROAD-Project/OpenSTA"
[11]: https://www.klayout.de/doc/manual/index.html?utm_source=chatgpt.com "KLayout User Manual"
