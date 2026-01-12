---
title: "实验数据作图python模板"
description: 
date: 2025-11-17T21:24:31+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
    - other
---

这是用iflow的glm模型生成的数据作图模板
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.interpolate import make_interp_spline
from typing import List, Dict, Tuple, Optional

# --- 设置Matplotlib支持中文显示 ---
plt.rcParams['font.sans-serif'] = ['WenQuanYi Zen Hei']
plt.rcParams['axes.unicode_minus'] = False

def plot_multiple_datasets_non_monotonic(
    datasets: List[Dict],
    x_label: str,
    y_label: str,
    title: str,
    smooth_method: str = 'spline',
    poly_degree: int = 3,
    save_path: str = None,
    figsize: Tuple[int, int] = (10, 6),
    colors: Optional[List[str]] = None,
    markers: Optional[List[str]] = None
):
    """
    一个支持多组数据同时绘制的函数，可以在同一张图上显示多组实验数据。
    此版本支持非递增（非单调）的x轴数据。

    参数:
    datasets (List[Dict]): 数据集列表，每个字典包含:
        - 'x_data': x轴数据
        - 'y_data': y轴数据
        - 'label': 该组数据的标签名称
        - 'smooth_method': (可选) 该组数据使用的平滑方法，默认使用全局设置
        - 'poly_degree': (可选) 该组数据的多项式阶数，默认使用全局设置
    x_label (str): x轴的标签名称（包含单位）
    y_label (str): y轴的标签名称（包含单位）
    title (str): 图表的标题
    smooth_method (str): 默认平滑曲线的方法 ('spline', 'polyfit' 或 'univariate_spline')
    poly_degree (int): 默认多项式阶数
    save_path (str): 图片保存路径
    figsize (Tuple[int, int]): 图表大小，默认 (10, 6)
    colors (List[str]): 自定义颜色列表
    markers (List[str]): 自定义标记样式列表
    """
    # 设置默认颜色和标记样式
    if colors is None:
        colors = ['blue', 'red', 'green', 'orange', 'purple', 'brown', 'pink', 'gray', 'olive', 'cyan']
    if markers is None:
        markers = ['o', 's', '^', 'D', 'v', '<', '>', 'p', '*', 'h']
    
    # 创建图表
    plt.figure(figsize=figsize)
    
    for i, dataset in enumerate(datasets):
        # 获取数据
        x = np.array(dataset['x_data'])
        y = np.array(dataset['y_data'])
        label = dataset['label']
        
        # 使用该组数据的设置或全局设置
        method = dataset.get('smooth_method', smooth_method)
        degree = dataset.get('poly_degree', poly_degree)
        
        # 选择颜色和标记
        color = colors[i % len(colors)]
        marker = markers[i % len(markers)]
        
        # 绘制原始数据点
        plt.scatter(x, y, label=f'{label} (原始数据)', color=color, marker=marker, zorder=5)
        
        # 生成平滑曲线
        x_smooth = np.linspace(x.min(), x.max(), 300)
        
        if method == 'spline':
            # 检查x轴数据是否单调递增
            is_monotonic = np.all(np.diff(x) > 0)
            
            if is_monotonic:
                # 如果数据是单调递增的，使用原始方法
                try:
                    spline = make_interp_spline(x, y, k=3)
                    y_smooth = spline(x_smooth)
                    plt.plot(x_smooth, y_smooth, color=color, linewidth=2, linestyle='--', alpha=0.8)
                except:
                    # 如果样条插值失败，回退到多项式拟合
                    print(f"警告: {label}的样条插值失败，使用多项式拟合代替")
                    coeffs = np.polyfit(x, y, deg=degree)
                    poly_func = np.poly1d(coeffs)
                    y_smooth = poly_func(x_smooth)
                    plt.plot(x_smooth, y_smooth, color=color, linewidth=2, linestyle='--', alpha=0.8)
            else:
                # 如果数据不是单调递增的，使用UnivariateSpline
                try:
                    from scipy.interpolate import UnivariateSpline
                    # 首先对x轴数据进行排序
                    sort_indices = np.argsort(x)
                    x_sorted = x[sort_indices]
                    y_sorted = y[sort_indices]
                    
                    # 使用UnivariateSpline处理非单调数据
                    spline = UnivariateSpline(x_sorted, y_sorted, s=0)
                    y_smooth = spline(x_smooth)
                    plt.plot(x_smooth, y_smooth, color=color, linewidth=2, linestyle='--', alpha=0.8)
                except:
                    # 如果UnivariateSpline也失败，回退到多项式拟合
                    print(f"警告: {label}的UnivariateSpline插值失败，使用多项式拟合代替")
                    coeffs = np.polyfit(x, y, deg=degree)
                    poly_func = np.poly1d(coeffs)
                    y_smooth = poly_func(x_smooth)
                    plt.plot(x_smooth, y_smooth, color=color, linewidth=2, linestyle='--', alpha=0.8)
                    
        elif method == 'polyfit':
            # 使用多项式拟合
            coeffs = np.polyfit(x, y, deg=degree)
            poly_func = np.poly1d(coeffs)
            y_smooth = poly_func(x_smooth)
            plt.plot(x_smooth, y_smooth, color=color, linewidth=2, linestyle='--', alpha=0.8)
            print(f"{label}的多项式拟合方程:\n{poly_func}\n")
        elif method == 'univariate_spline':
            # 直接使用UnivariateSpline方法
            try:
                from scipy.interpolate import UnivariateSpline
                # 首先对x轴数据进行排序
                sort_indices = np.argsort(x)
                x_sorted = x[sort_indices]
                y_sorted = y[sort_indices]
                
                spline = UnivariateSpline(x_sorted, y_sorted, s=0)
                y_smooth = spline(x_smooth)
                plt.plot(x_smooth, y_smooth, color=color, linewidth=2, linestyle='-', alpha=0.8)
            except:
                # 如果UnivariateSpline失败，回退到多项式拟合
                print(f"警告: {label}的UnivariateSpline插值失败，使用多项式拟合代替")
                coeffs = np.polyfit(x, y, deg=degree)
                poly_func = np.poly1d(coeffs)
                y_smooth = poly_func(x_smooth)
                plt.plot(x_smooth, y_smooth, color=color, linewidth=2, linestyle='--', alpha=0.8)
    
    # 添加图表元素
    plt.title(title, fontsize=16, fontweight='bold')
    plt.xlabel(x_label, fontsize=12)
    plt.ylabel(y_label, fontsize=12)
    plt.grid(True, linestyle='--', alpha=0.6)
    plt.legend(bbox_to_anchor=(1.05, 1), loc='upper left')  # 将图例放在图表外部
    
    # 调整布局，防止标签被裁切
    plt.tight_layout()
    
    # 保存图表
    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight')
        print(f"图表已保存至: {save_path}")
    
    plt.show()

def plot_comparison_chart_non_monotonic(
    datasets: List[Dict],
    x_label: str,
    y_label: str,
    title: str,
    save_path: str = None,
    plot_type: str = 'line'
):
    """
    简化版的多组数据对比图函数，适用于快速对比。
    此版本支持非递增（非单调）的x轴数据。

    参数:
    datasets (List[Dict]): 数据集列表，每个字典包含:
        - 'x_data': x轴数据
        - 'y_data': y轴数据
        - 'label': 该组数据的标签名称
    x_label (str): x轴的标签名称
    y_label (str): y轴的标签名称
    title (str): 图表的标题
    save_path (str): 图片保存路径
    plot_type (str): 绘图类型 ('line' 线图, 'bar' 柱状图, 'scatter' 散点图)
    """
    plt.figure(figsize=(10, 6))
    colors = ['blue', 'red', 'green', 'orange', 'purple', 'brown', 'pink', 'gray']
    
    for i, dataset in enumerate(datasets):
        x = np.array(dataset['x_data'])
        y = np.array(dataset['y_data'])
        label = dataset['label']
        color = colors[i % len(colors)]
        
        if plot_type == 'line':
            # 对于线图，如果数据不是单调的，我们需要对数据进行排序
            is_monotonic = np.all(np.diff(x) >= 0)
            if not is_monotonic:
                sort_indices = np.argsort(x)
                x_sorted = x[sort_indices]
                y_sorted = y[sort_indices]
                plt.plot(x_sorted, y_sorted, marker='o', label=label, color=color, linewidth=2)
            else:
                plt.plot(x, y, marker='o', label=label, color=color, linewidth=2)
        elif plot_type == 'bar':
            # 柱状图需要特殊处理，确保x轴数据对齐
            width = 0.8 / len(datasets)
            x_pos = x + (i - len(datasets)/2 + 0.5) * width
            plt.bar(x_pos, y, width, label=label, color=color, alpha=0.7)
        elif plot_type == 'scatter':
            plt.scatter(x, y, label=label, color=color, s=50, alpha=0.7)
    
    plt.title(title, fontsize=16, fontweight='bold')
    plt.xlabel(x_label, fontsize=12)
    plt.ylabel(y_label, fontsize=12)
    plt.grid(True, linestyle='--', alpha=0.6)
    plt.legend()
    
    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight')
        print(f"图表已保存至: {save_path}")
    
    plt.show()

# --- 使用示例 ---
if __name__ == "__main__":
    # 测试数据：包含非单调递增的x轴数据
    datasets=[
        {
            'x_data':[5.50,4.90,4.42,3.66,2.75,1.85,0.039],  # 递减数据
            'y_data':[0,6.05,10.71,18.43,27.52,36.44,54.32],
            'label':'设计网络',
            'smooth_method':'spline'
        },
        {
            'x_data':[5.50,4.89,4.40,3.65,2.74,1.84,0.030],  # 递减数据
            'y_data':[0,6.03,10.72,18.35,27.35,36.14,53.88],
            'label':'等效网络',
            'smooth_method':'spline'
        }
    ]
    
    # 测试非单调数据（先增后减）
    non_monotonic_datasets = [
        {
            'x_data':[1, 2, 3, 4, 5, 4, 3, 2, 1],  # 非单调数据
            'y_data':[1, 4, 9, 16, 25, 16, 9, 4, 1],
            'label':'非单调测试数据',
            'smooth_method':'univariate_spline'
        }
    ]
    
    print("=== 测试递减数据 ===")
    plot_multiple_datasets_non_monotonic(
        datasets=datasets,
        x_label='U(V)',
        y_label='I(mA)',
        title='设计网络域等效网络的端口特性对比（支持非递增数据）',
        save_path='non_monotonic_1.png'
    )
    
    print("\n=== 测试非单调数据 ===")
    plot_multiple_datasets_non_monotonic(
        datasets=non_monotonic_datasets,
        x_label='X值',
        y_label='Y值',
        title='非单调数据测试',
        save_path='non_monotonic_test.png'
    )
```