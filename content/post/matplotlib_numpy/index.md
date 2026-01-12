---
title: "Matplotlib_numpy(created by gemini)"
description: 
date: 2025-10-27T16:23:21+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
    - other
tags:
    - python
    - ai-creation

---



下面我为你总结一套工业级、学术界都广泛使用的Matplotlib作图“套路”，以及与之配合的NumPy常用函数。这套流程能覆盖你80%以上的作图需求，并让你的图表清晰、美观、信息量丰富。

---

### 核心理念：“面向对象”的作图范式

忘掉零散的`plt.plot()`、`plt.title()`。从现在开始，拥抱“**Figure与Axes**”的面向对象范式。这是最重要的一步。

**基本模板（所有作图的起点）：**
```python
import matplotlib.pyplot as plt
import numpy as np

# --- 1. 创建画布(Figure)和子图(Axes) ---
# fig是整个画布，ax是具体的绘图区域
# nrows=1, ncols=1 表示创建一个1x1的网格，即只有一个子图
fig, ax = plt.subplots(nrows=1, ncols=1, figsize=(10, 6)) # figsize控制画布大小

# --- 2. 在Axes上绘图 ---
# 所有绘图操作都通过ax对象完成，而不是plt
# x_data, y_data = ... (你的数据)
# ax.plot(x_data, y_data, label='My Data') 

# --- 3. 精细化定制Axes ---
ax.set_title("This is the Title", fontsize=16)
ax.set_xlabel("X-axis Label", fontsize=12)
ax.set_ylabel("Y-axis Label", fontsize=12)
ax.grid(True, linestyle='--', alpha=0.6) # 添加网格线
ax.legend(fontsize=10) # 显示图例

# --- 4. 调整布局并显示 ---
plt.tight_layout() # 自动调整子图参数，使之填充整个图像区域
plt.show()
```
**记住这个模板，你就掌握了套路的精髓。**

---

### NumPy & Matplotlib 常用函数与作图套路

下面我将作图需求分为几大类，每一类都提供一个“套路”和相关的常用函数。

#### 套路一：单线/多线对比图 (最常用)

**场景**：对比滤波前后的信号、不同参数下的模型性能、理论值与实际值。

**流程**：
1.  用`np.linspace`或`np.arange`创建统一的X轴数据。
2.  在**同一个`ax`对象上多次调用`plot()`**。
3.  通过`label`参数区分不同曲线，最后用`ax.legend()`显示图例。

**代码范例**：
```python
# --- 数据准备 (NumPy) ---
fs = 1000
t = np.linspace(0, 1, fs, endpoint=False) # 0到1秒的时间轴
signal_clean = np.sin(2 * np.pi * 5 * t)
noise = 0.5 * np.random.randn(len(t))
signal_noisy = signal_clean + noise

# --- Matplotlib 套路 ---
fig, ax = plt.subplots(figsize=(12, 6))

ax.plot(t, signal_noisy, label='Noisy Signal', color='blue', alpha=0.5)
ax.plot(t, signal_clean, label='Clean Signal', color='red', linewidth=2, linestyle='-')

# 精细化控制
ax.set_title("Comparison of Signals")
ax.set_xlabel("Time [s]")
ax.set_ylabel("Amplitude")
ax.set_xlim(0, 0.5) # 常用：放大X轴的某个区域
ax.grid(True)
ax.legend()

plt.tight_layout()
plt.show()
```

**常用NumPy函数**：
*   `np.linspace(start, stop, num)`: 创建等差数列，常用于时间轴或平滑的X轴。
*   `np.arange(start, stop, step)`: 按步长创建数组，常用于离散序列。
*   `np.sin`, `np.cos`, `np.exp`: 生成各种数学函数波形。
*   `np.random.randn(size)`: 生成标准正态分布（高斯）噪声。

#### 套路二：多子图布局 (信息维度扩展)

**场景**：同时展示时域和频域、输入和输出、多个实验结果。

**流程**：
1.  在`plt.subplots()`中指定`nrows`和`ncols`。
2.  `subplots`会返回一个**`axes`数组**。通过索引（如`ax[0]`, `ax[1]`或`ax[0, 1]`）来选择要在哪个子图上绘图。
3.  可以设置`sharex=True`或`sharey=True`来共享坐标轴，使对比更直观。

**代码范例** (时域 vs 频域)：
```python
from scipy.fft import fft, fftfreq

# --- 数据准备 (NumPy & SciPy) ---
fs = 1000
t = np.linspace(0, 1, fs, endpoint=False)
signal = np.sin(2 * np.pi * 50 * t) + 0.5 * np.sin(2 * np.pi * 120 * t)

# FFT计算
N = len(signal)
yf = fft(signal)
xf = fftfreq(N, 1 / fs)[:N//2] # 获取频率轴

# --- Matplotlib 套路 (1行2列) ---
fig, axes = plt.subplots(nrows=2, ncols=1, figsize=(10, 8), sharex=False)

# 在第一个子图 (axes[0]) 上画时域图
axes[0].plot(t, signal)
axes[0].set_title("Time Domain")
axes[0].set_xlabel("Time [s]")
axes[0].set_ylabel("Amplitude")
axes[0].grid(True)
axes[0].set_xlim(0, 0.2)

# 在第二个子图 (axes[1]) 上画频域图
axes[1].plot(xf, 2.0/N * np.abs(yf[0:N//2]))
axes[1].set_title("Frequency Domain (FFT)")
axes[1].set_xlabel("Frequency [Hz]")
axes[1].set_ylabel("Amplitude")
axes[1].grid(True)
axes[1].set_xlim(0, 200)

plt.tight_layout()
plt.show()
```

#### 套路三：数据分布与统计图 (直方图/散点图)

**场景**：分析噪声分布、变量之间的相关性。

**流程**：
1.  **直方图**使用`ax.hist()`，可以直观地看到数据分布的形状。
2.  **散点图**使用`ax.scatter()`，用于观察两个变量之间的关系。

**代码范例** (直方图)：
```python
# --- 数据准备 (NumPy) ---
# 生成均值为5，标准差为2的高斯分布数据
data = 5 + 2 * np.random.randn(1000)

# --- Matplotlib 套路 ---
fig, ax = plt.subplots(figsize=(8, 5))

# bins控制柱子的数量，density=True表示归一化为概率密度
ax.hist(data, bins=30, density=True, alpha=0.7, label='Histogram')

# 常用：在直方图上叠加理论分布曲线
from scipy.stats import norm
xmin, xmax = plt.xlim()
x = np.linspace(xmin, xmax, 100)
p = norm.pdf(x, 5, 2) # 理论概率密度函数
ax.plot(x, p, 'k', linewidth=2, label='Theoretical PDF')

ax.set_title("Data Distribution")
ax.set_xlabel("Value")
ax.set_ylabel("Density")
ax.legend()
ax.grid(True)

plt.tight_layout()
plt.show()
```

**常用NumPy函数**：
*   `np.mean(data)`, `np.std(data)`: 计算均值和标准差。
*   `np.random.rand(size)`: [0, 1)均匀分布。
*   `np.corrcoef(x, y)`: 计算相关系数矩阵。

#### 套路四：添加辅助线与注释 (让图表“说话”)

**场景**：标记阈值、截止频率、特定事件点、理论极限。

**流程**：
1.  `ax.axvline()`: 画垂直线。
2.  `ax.axhline()`: 画水平线。
3.  `ax.axvspan()`: 垂直区域着色。
4.  `ax.text()` 或 `ax.annotate()`: 添加文字注释。

**代码范例** (频率响应图标注)：
```python
# 假设 freq_hz, magnitude_db 是频率响应数据
# ...

fig, ax = plt.subplots(figsize=(10, 6))
ax.plot(freq_hz, magnitude_db)
ax.set_title("Filter Frequency Response")
ax.set_xlabel("Frequency [Hz]")
ax.set_ylabel("Magnitude [dB]")

# --- 添加辅助线 ---
cutoff_freq = 50
# 标记-3dB线（半功率点）
ax.axhline(-3, color='red', linestyle='--', label='-3 dB Cutoff')
# 标记名义截止频率
ax.axvline(cutoff_freq, color='green', linestyle=':', label=f'Nominal Cutoff = {cutoff_freq} Hz')
# 标记通带区域
ax.axvspan(0, 40, color='gray', alpha=0.2, label='Passband Region')

ax.set_ylim(-60, 5)
ax.grid(True)
ax.legend()
plt.tight_layout()
plt.show()
```

### 总结：你的“作图武器库”

| 需求分类 | 核心Matplotlib函数 | 常用NumPy函数 | 套路精髓 |
| :--- | :--- | :--- | :--- |
| **基础框架** | `fig, ax = plt.subplots()` | `np.array` | **面向对象**，分离画布与绘图区 |
| **曲线对比** | `ax.plot()`, `ax.legend()` | `np.linspace`, `np.arange` | 同一`ax`多次`plot`，用`label`区分 |
| **多维展示** | `plt.subplots(nrows, ncols)` | `fft` (from scipy) | 索引`axes`数组，用`sharex/y`联动 |
| **数据分布** | `ax.hist()`, `ax.scatter()` | `np.random`, `np.mean`, `np.std` | 选择合适的图表类型展示数据内在结构 |
| **信息高亮** | `ax.axvline`, `ax.axhline`, `ax.axvspan` | - | 用辅助线和注释引导读者关注重点 |
| **全局美化** | `plt.tight_layout()` | - | 自动调整布局，防止标签重叠 |

将这套流程和范例代码保存在你的笔记中，每次作图时，先思考你的“故事”需要哪种套路，然后填充数据和细节。很快，你就能随心所欲地创作出专业且富有洞察力的图表了。