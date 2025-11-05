---
title: "玻尔兹曼统计"
description: 
date: 2025-11-05T20:16:11+08:00
image: 
math: true
license: 
hidden: false
comments: false
draft: false
---
好的，我们按照这个思路重新整理一份更聚焦于公式和具体计算结论的版本。

---

### 一、玻尔兹曼统计的适用范围

*   **适用体系**：由大量、弱相互作用、经典可分辨的粒子组成的宏观系统。
*   **适用条件**：高温、低密度，即处于**非简并**状态 ($n\lambda_T^3 \ll 1$)。

### 二、核心工具：配分函数 Z (The Master Key)

*   **单粒子配分函数 (能级求和)**:
    $$ z = \sum_s g_s e^{-\beta \epsilon_s} \quad (\text{其中} \beta = \frac{1}{k_B T}) $$
    
*   **单粒子配分函数 (能量积分)**:
    $$ z = \int_0^\infty g(E) e^{-\beta E} dE \quad (g(E) \text{是能态密度}) $$

*   **N 粒子体系配分函数**:
    *   **可分辨粒子系统 (如固体原子)**：$Z = z^N$
    *   **不可分辨粒子系统 (如气体分子)**：$Z = \frac{z^N}{N!}$ (修正吉布斯佯谬)

### 三、由配分函数导出一切热力学量

一旦求出总配分函数 `Z`，所有宏观量都可以通过以下公式计算得出。这是**最重要的计算桥梁**。

*   **亥姆霍兹自由能 F**:
    $$ F = -k_B T \ln Z $$

*   **熵 S**:
    $$ S = -(\frac{\partial F}{\partial T})_{V,N} = k_B \ln Z + k_B T (\frac{\partial \ln Z}{\partial T})_{V,N} $$
    (或者用 $S=(U-F)/T$ 更方便)

*   **压强 P**:
    $$ P = -(\frac{\partial F}{\partial V})_{T,N} = k_B T (\frac{\partial \ln Z}{\partial V})_{T,N} $$

*   **内能 U**:
    $$ U = -(\frac{\partial \ln Z}{\partial \beta})_{V,N} = N k_B T^2 \frac{\partial \ln z}{\partial T} $$

*   **热容 $C_V$**:
    $$ C_V = (\frac{\partial U}{\partial T})_{V,N} $$

*   **化学势 $\mu$**:
    $$ \mu = (\frac{\partial F}{\partial N})_{T,V} = -k_B T (\frac{\partial \ln Z}{\partial N})_{T,V} $$

---

### 四、麦克斯韦速率/速度分布律 (公式集)

这是玻尔兹曼分布在理想气体平动上的具体应用。

1.  **麦克斯韦速率分布函数 $f(v)$**: (v是速率，$v \in [0, \infty)$)
    $$ f(v) = 4\pi (\frac{m}{2\pi k_B T})^{3/2} v^2 e^{-mv^2 / 2k_B T} $$
    **归一化条件**: $\int_0^\infty f(v) dv = 1$

2.  **三种特征速率**:
    *   **最概然速率 $v_p$ (峰值速率)**:
        $$ v_p = \sqrt{\frac{2k_B T}{m}} $$
    *   **平均速率 $\bar{v}$ (统计平均)**:
        $$ \bar{v} = \sqrt{\frac{8k_B T}{\pi m}} $$
    *   **方均根速率 $v_{rms}$ (与平均动能相关)**:
        $$ v_{rms} = \sqrt{\frac{3k_B T}{m}} $$
    **大小关系 (常考选择题)**: $v_p < \bar{v} < v_{rms}$

---

### 五、理想气体分子自由度的热力学性质 (公式集)

#### 1. 平动自由度 (适用于所有气体)
*   **单粒子配分函数**:
    $$ z_{tr} = V (\frac{2\pi m k_B T}{h^2})^{3/2} \propto V T^{3/2} $$
*   **内能**:
    $$ U_{tr} = \frac{3}{2} N k_B T $$
*   **热容**:
    $$ C_{V, tr} = \frac{3}{2} N k_B $$
*   **熵 (Sackur-Tetrode 公式)**:
    $$ S_{tr} = N k_B [\ln(\frac{V}{N}) + \frac{3}{2}\ln T + \frac{3}{2}\ln(\frac{2\pi m k_B}{h^2}) + \frac{5}{2}] $$

#### 2. 转动自由度 (适用于双原子或多原子分子)
*   **单粒子配分函数 (高温近似)**:
    $$ z_{rot} \approx \frac{2I k_B T}{\sigma \hbar^2} \propto T \quad (I \text{为转动惯量}, \sigma \text{为对称因子, 双原子}\sigma=2) $$
*   **内能**:
    $$ U_{rot} = N k_B T $$
*   **热容**:
    $$ C_{V, rot} = N k_B $$
*   **熵**:
    $$ S_{rot} = N k_B [\ln z_{rot} + 1] $$

#### 3. 振动自由度 (高温下双原子或多原子分子)
*   **单粒子配分函数 (谐振子模型)**:
    $$ z_{vib} = \frac{1}{1-e^{-\hbar\omega/k_B T}} = \frac{e^{-\Theta_v/2T}}{1-e^{-\Theta_v/T}} \quad (\Theta_v=\hbar\omega/k_B \text{是振动特征温度}) $$
*   **内能 (能量零点取在基态 $\frac{1}{2}\hbar\omega$)**:
    $$ U_{vib} = N \frac{\hbar\omega}{e^{\hbar\omega/k_B T} - 1} $$
*   **热容**:
    $$ C_{V, vib} = N k_B (\frac{\hbar\omega}{k_B T})^2 \frac{e^{\hbar\omega/k_B T}}{(e^{\hbar\omega/k_B T}-1)^2} = N k_B (\frac{\Theta_v}{T})^2 \frac{e^{\Theta_v/T}}{(e^{\Theta_v/T}-1)^2} $$
    *   **高温极限 ($T \gg \Theta_v$)**: $C_{V, vib} \to N k_B$
    *   **低温极限 ($T \ll \Theta_v$)**: $C_{V, vib} \to 0$
*   **熵**:
    $$ S_{vib} = N k_B [-\ln(1-e^{-\Theta_v/T}) + \frac{\Theta_v}{T}\frac{1}{e^{\Theta_v/T}-1}] $$

---

### 六、固体热容 (公式集)

#### 1. 经典杜隆-珀替定律
*   **模型**: N 个原子在晶格上做三维简谐振动，按能量均分定理处理。
*   **内能**: $U = 3N k_B T$
*   **热容**:
    $$ C_V = 3 N k_B \approx 3R \quad (\text{对 1 摩尔固体}) $$
*   **结论**: 经典理论预言热容为常数，与高温实验相符，但在低温下失效。

#### 2. 爱因斯坦模型
*   **模型**: N 个原子看作 3N 个**频率相同** ($\omega_E$) 的、**独立**的量子谐振子。
*   **配分函数**: $Z = (z_{vib, 1D})^{3N}$
*   **内能**:
    $$ U = 3N \frac{\hbar\omega_E}{e^{\hbar\omega_E/k_B T}-1} $$
*   **热容**:
    $$ C_V = 3 N k_B (\frac{\Theta_E}{T})^2 \frac{e^{\Theta_E/T}}{(e^{\Theta_E/T}-1)^2} \quad (\Theta_E=\hbar\omega_E/k_B \text{是爱因斯坦温度}) $$
*   **结论**:
    *   **高温极限 ($T \gg \Theta_E$)**: $C_V \to 3N k_B$ (符合杜隆-珀替定律)。
    *   **低温极限 ($T \ll \Theta_E$)**: $C_V \propto e^{-\Theta_E/T}$ (**指数形式**下降)，能解释热容趋于0，但与实验的 $T^3$ 规律不符。

#### 3. 德拜模型
*   **模型**: 把晶格振动看作晶体中的弹性波（声子），有频率分布，且有最高截止频率 $\omega_D$ (德拜频率)。
*   **态密度**: $g(\omega) = \frac{9N}{\omega_D^3}\omega^2 \quad (0 \le \omega \le \omega_D)$
*   **内能 (积分形式)**:
    $$ U = \int_0^{\omega_D} \frac{\hbar\omega}{e^{\hbar\omega/k_B T}-1} g(\omega) d\omega $$
*   **热容**:
    $$ C_V = 9N k_B (\frac{T}{\Theta_D})^3 \int_0^{\Theta_D/T} \frac{x^4 e^x}{(e^x-1)^2} dx \quad (x=\hbar\omega/k_BT, \Theta_D=\hbar\omega_D/k_B \text{是德拜温度}) $$
*   **结论 (最重要)**:
    *   **高温极限 ($T \gg \Theta_D$)**: $C_V \to 3N k_B$ (符合杜隆-珀替定律)。
    *   **低温极限 ($T \ll \Theta_D$)**:
        $$ C_V = \frac{12\pi^4}{5} N k_B (\frac{T}{\Theta_D})^3 \propto T^3 $$
        这就是**德拜 $T^3$ 定律**，与低温实验符合得非常好。