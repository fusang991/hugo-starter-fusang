---
title: "Zynq Ps Port"
description: 
date: 2025-10-24T19:59:11+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
---
Vivado 中的约束（Constraints）主要通过 **Xilinx Design Constraints (XDC)** 文件来定义，其语法基于 **Tcl（Tool Command Language）**，并扩展了用于时序、管脚分配、物理约束等的专用命令。以下是 Vivado 约束的基本语法和常用命令分类：

---

### 1. **管脚分配（Pin Assignment）**
指定顶层模块端口对应的 FPGA 引脚：

```tcl
set_property PACKAGE_PIN <pin_name> [get_ports <port_name>]
set_property IOSTANDARD LVCMOS33 [get_ports <port_name>]
```

示例：
```tcl
set_property PACKAGE_PIN D18 [get_ports clk]
set_property IOSTANDARD LVCMOS33 [get_ports clk]
```

---

### 2. **时钟约束（Clock Constraints）**
定义主时钟、生成时钟或虚拟时钟。

#### 主时钟（Primary Clock）：
```tcl
create_clock -name <clk_name> -period <period_ns> [get_ports <port_name>]
```

示例（100 MHz 时钟）：
```tcl
create_clock -name sys_clk -period 10.000 [get_ports clk]
```

#### 生成时钟（Generated Clock）：
用于 PLL/MMCM 输出或分频/倍频时钟：
```tcl
create_generated_clock -name <gen_clk_name> -source [get_pins <source_pin>] -divide_by <N> [get_pins <output_pin>]
```

示例（2 分频）：
```tcl
create_generated_clock -name clk_div2 -source [get_pins clk] -divide_by 2 [get_pins clk_div2_reg/Q]
```

---

### 3. **输入/输出延迟约束（I/O Delay）**
用于指定外部器件与 FPGA 之间的时序关系。

#### 输入延迟：
```tcl
set_input_delay -clock <clk_name> <delay_ns> [get_ports <input_port>]
```

#### 输出延迟：
```tcl
set_output_delay -clock <clk_name> <delay_ns> [get_ports <output_port>]
```

示例：
```tcl
set_input_delay -clock sys_clk 2.0 [get_ports data_in]
set_output_delay -clock sys_clk 1.5 [get_ports data_out]
```

---

### 4. **时序例外（Timing Exceptions）**
用于覆盖默认时序分析，如多周期路径、伪路径等。

#### 伪路径（False Path）：
```tcl
set_false_path -from [get_pins <start_point>] -to [get_pins <end_point>]
```

或针对端口：
```tcl
set_false_path -from [get_ports async_in]
```

#### 多周期路径（Multicycle Path）：
```tcl
set_multicycle_path <num_cycles> -setup -from [get_clocks clk1] -to [get_clocks clk2]
set_multicycle_path <num_cycles> -hold  -from [get_clocks clk1] -to [get_clocks clk2]
```

---

### 5. **物理约束（Physical Constraints）**
如区域约束（Pblock）、布局锁定等（较少在 XDC 中直接写，但支持）：

```tcl
set_property LOC SLICE_X10Y20 [get_cells my_reg]
```

---

### 注意事项：
- **顺序敏感**：XDC 文件按顺序执行，后面的约束可能覆盖前面的。
- **对象获取**：常用 `[get_ports ...]`、`[get_pins ...]`、`[get_cells ...]`、`[get_clocks ...]` 来定位设计对象。
- **单位**：时间单位默认为纳秒（ns），可省略。
- **注释**：使用 `#` 开头。

---

### 示例完整 XDC 片段：
```tcl
# Clock
create_clock -name clk -period 10.000 [get_ports clk]

# Pin assignment
set_property PACKAGE_PIN E3 [get_ports clk]
set_property IOSTANDARD LVCMOS33 [get_ports clk]

set_property PACKAGE_PIN D9 [get_ports reset_n]
set_property IOSTANDARD LVCMOS33 [get_ports reset_n]

# Input delay
set_input_delay -clock clk 2.0 [get_ports data_in]

# False path for async reset
set_false_path -from [get_ports reset_n]
```

---

如果你正在编写状态机并关注复位信号（如 `rst`），通常建议：
- 若为同步复位，无需特殊约束；
- 若为异步复位，需用 `set_false_path` 或 `set_input_delay` 明确其时序不参与分析，或使用 `create_clock` + `set_false_path` 组合处理。

需要更具体的约束示例（比如针对你的状态机复位）可以告诉我。