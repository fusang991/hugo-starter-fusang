---
title: "数理方程分离变量法"
description: 
date: 2025-11-21T15:47:53+08:00
image: 
math: true
license: 
hidden: false
comments: false
draft: false
categories:
    - other
---
这是一份非常系统的总结需求。掌握这四种情况，基本上就覆盖了本科数理方程考试中**分离变量法** 90% 的考点。

核心逻辑是：
1.  **边界条件**决定了**固有函数（基底）**的形式（是 $\sin$ 还是 $\cos$）。
2.  **方程类型**决定了**时间/另一维度函数**的形式（是振荡、衰减还是双曲）。

---

### 第一部分：一维演化方程（弦振动 vs 热传导）

这两种方程的**空间部分（$x$）处理方式完全一样**，区别仅在于**时间部分（$t$）**。

*   **弦振动 (Wave):** $u_{tt} = a^2 u_{xx}$ $\rightarrow$ 时间是振荡的 ($\sin \omega t, \cos \omega t$)，甚至有线性增长 ($t$)。
*   **热传导 (Heat):** $u_t = a^2 u_{xx}$ $\rightarrow$ 时间是衰减的 ($e^{-k^2 t}$)，或者是常数。

#### 1. 两端固定 (Dirichlet-Dirichlet)
*   **BC:** $u(0)=0, u(l)=0$
*   **基底:** $\sin \frac{n\pi x}{l}$
*   **弦振动解:**
    $$ u = \sum_{n=1}^\infty \left( C_n \cos \frac{n\pi a t}{l} + D_n \sin \frac{n\pi a t}{l} \right) \sin \frac{n\pi x}{l} $$
*   **热传导解:**
    $$ u = \sum_{n=1}^\infty C_n e^{-\left(\frac{n\pi a}{l}\right)^2 t} \sin \frac{n\pi x}{l} $$

#### 2. 两端自由 (Neumann-Neumann) —— **高频考点**
*   **BC:** $u_x(0)=0, u_x(l)=0$
*   **基底:** $1$ (对应 $n=0$) 和 $\cos \frac{n\pi x}{l}$
*   **弦振动解 (含刚体位移):**
    $$ u = \underbrace{C_0 + D_0 t}_{\color{red}{n=0 \text{项}}} + \sum_{n=1}^\infty \left( C_n \cos \dots + D_n \sin \dots \right) \cos \frac{n\pi x}{l} $$
*   **热传导解 (含稳态解):**
    $$ u = \underbrace{C_0}_{\color{red}{\text{最终平衡温度}}} + \sum_{n=1}^\infty C_n e^{-\left(\frac{n\pi a}{l}\right)^2 t} \cos \frac{n\pi x}{l} $$

#### 3. 一固一游 (Mixed)
*   **BC:** $u(0)=0, u_x(l)=0$
*   **基底:** $\sin \frac{(2n+1)\pi x}{2l}$ (半奇数倍)
*   **弦振动解:**
    $$ u = \sum_{n=0}^\infty (T_n(t)) \sin \frac{(2n+1)\pi x}{2l} $$
*   **热传导解:**
    $$ u = \sum_{n=0}^\infty C_n e^{-\left(\frac{(2n+1)\pi a}{2l}\right)^2 t} \sin \frac{(2n+1)\pi x}{2l} $$

---

### 第二部分：二维拉普拉斯方程（直角坐标）
方程：$u_{xx} + u_{yy} = 0$

**解题铁律：**
1.  看边界：哪两个平行的边界条件是 **0（齐次）**？
2.  **齐次方向**选三角函数 ($\sin, \cos$) $\rightarrow$ 产生固有值 $\lambda_n$。
3.  **非齐次方向**选双曲函数 ($\sinh, \cosh$) 或指数函数 ($e^y, e^{-y}$) $\rightarrow$ 为了凑边界。

#### 情形 A：左右为 0 ($x=0, x=a$ 是齐次)
*   $X(x)$ 方向：$\sin \frac{n\pi x}{a}$
*   $Y(y)$ 方向：$A_n \cosh \frac{n\pi y}{a} + B_n \sinh \frac{n\pi y}{a}$ (或者 $C_n e^{\dots} + D_n e^{-\dots}$)
*   **解的形式：**
    $$ u(x,y) = \sum_{n=1}^\infty \left( A_n \cosh \frac{n\pi y}{a} + B_n \sinh \frac{n\pi y}{a} \right) \sin \frac{n\pi x}{a} $$
    *(系数 $A_n, B_n$ 由上下两边的非齐次条件定)*

#### 情形 B：上下为 0 ($y=0, y=b$ 是齐次)
*   $Y(y)$ 方向：$\sin \frac{n\pi y}{b}$
*   $X(x)$ 方向：双曲函数
*   **解的形式：**
    $$ u(x,y) = \sum_{n=1}^\infty \left( A_n \cosh \frac{n\pi x}{b} + B_n \sinh \frac{n\pi x}{b} \right) \sin \frac{n\pi y}{b} $$

#### 情形 C：四周都有值 (叠加原理)
*   如果矩形四边都不是 0，必须把问题拆成两个子问题（A + B），分别求解后再相加。

---

### 第三部分：二维拉普拉斯方程（极坐标）
方程：$\Delta u = 0$
通用原料：$r^n, r^{-n}, \ln r, \text{const}$ 和 $\sin n\theta, \cos n\theta$。

**分类依据：区域是否包含圆心 ($0$) 和无穷远 ($\infty$)。**

#### 1. 圆内问题 ($r < a$)
*   **去除项：** $\ln r$ (在0处无穷), $r^{-n}$ (在0处无穷)
*   **解的形式：**
    $$ u(r,\theta) = \frac{A_0}{2} + \sum_{n=1}^\infty r^n (A_n \cos n\theta + B_n \sin n\theta) $$
*   *(这是最常用的泊松积分公式的前身)*

#### 2. 圆外问题 ($r > a$)
*   **去除项：** $r^n$ (在$\infty$处发散), $\ln r$ (通常去除，除非有源)
*   **解的形式：**
    $$ u(r,\theta) = \frac{A_0}{2} + \sum_{n=1}^\infty \frac{1}{r^n} (A_n \cos n\theta + B_n \sin n\theta) $$

#### 3. 圆环问题 ($a < r < b$)
*   **去除项：** 无。所有项都保留。
*   **解的形式：**
    $$ u(r,\theta) = A_0 + B_0 \ln r + \sum_{n=1}^\infty (C_n r^n + D_n r^{-n})(A_n \cos n\theta + B_n \sin n\theta) $$

---

### 终极速查表 (Cheat Sheet)

| 方程与坐标 | 关键特征 | 解的主要形式 (级数项) | 备注 |
| :--- | :--- | :--- | :--- |
| **弦振动 (1D)** | 波动 | $T(t) \cdot X(x)$ | $X(x)$ 定基底，**$T(t)$ 是正余弦** |
| **热传导 (1D)** | 扩散 | $T(t) \cdot X(x)$ | $X(x)$ 定基底，**$T(t)$ 是指数衰减** |
| **拉普拉斯 (直角)** | 稳态 | Trig(齐次向) $\cdot$ Hyperbolic(非齐次向) | **一边 $\sin$，另一边 $\sinh/\cosh$** |
| **拉普拉斯 (圆内)** | 极坐标 | $r^n \cdot (A\cos + B\sin)$ | 去掉 $r^{-n}$ 和 $\ln r$ |
| **拉普拉斯 (圆外)** | 极坐标 | $r^{-n} \cdot (A\cos + B\sin)$ | 去掉 $r^n$ |

**做题套路总结：**
1.  **定方程：** 决定了时间项 ($T$) 还是双曲项 ($Y$)。
2.  **看边界 (x/齐次方向)：**
    *   $0, 0 \rightarrow \sin$
    *   $0', 0' \rightarrow \cos$ (注意 $n=0$ 常数项)
    *   $0, 0' \rightarrow \sin((n+0.5)\dots)$
3.  **看区域 (极坐标)：**
    *   有圆心 $\rightarrow$ 留 $r^n$
    *   有无穷 $\rightarrow$ 留 $r^{-n}$