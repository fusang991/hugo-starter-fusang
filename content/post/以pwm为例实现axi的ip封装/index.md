---
title: "以pwm为例实现axi的ip封装(created by gemini)"
description: 
date: 2025-10-23T22:40:54+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
tags:
  - ai-creation
  - zynq
  - axi
categories:
  - zynq
---
太棒了！让我们从绝对的第一性原理出发，彻底搞懂AXI，然后一步步将它与你的PWM设计融合，完成一次工业级的IP封装。

### 第一性原理：AXI是什么，它到底解决了什么问题？

想象一下，你的Zynq芯片里有一个**国王（CPU / PS）**和很多**工匠（你的PWM模块、DMA、GPIO等 / PL）**。

1.  **问题**：国王只有一个，但他需要同时对很多工匠下达指令（“PWM模块，把周期设为1000”，“DMA，把这段数据搬走”）。如果每个工匠都有一根专线连到国王那里，那国王身上就要插无数根线，这显然不现实。

2.  **解决方案**：建立一个**“中央邮政系统”**。所有工匠都在这个系统里注册一个唯一的**“邮箱地址”**。国王想给谁下指令，只需要写一封信，写上“收件人地址”和“信件内容”，然后扔进邮政系统。邮政系统会负责把信送到正确的工匠手里。

这个**“中央邮政系统”**，在我们的芯片世界里，就是**总线（Bus）**。
这个**“写信、送信、收信的规则”**，就是**协议（Protocol）**。

**AXI (Advanced eXtensible Interface) 就是ARM公司定义的一套非常流行、非常高效的“邮政系统规则”。**

### AXI4-Lite: 轻量级的信件服务

AXI有很多版本，我们封装PWM这样的控制模块，只需要最简单的一种：**AXI4-Lite**。你可以把它理解为专门用来收发**“短信”或“明信片”**的系统，每次只传递少量（32位/64位）的数据，非常适合用来读写配置寄存器。

#### AXI的核心交互：握手（Handshake）

AXI协议的灵魂是**“VALID/READY”握手机制**。忘掉所有复杂的信号，只记住这一点：

*   **VALID (有效信号)**: 由**发送方**控制。当发送方把数据/地址准备好后，它就拉高`VALID`，大声宣布：“我的信息已经准备好了，并且稳定不变！”
*   **READY (就绪信号)**: 由**接收方**控制。当接收方有能力接收信息时，它就拉高`READY`，大声回应：“我准备好了，请把信息发给我！”

**黄金法则：** 只有在一个时钟上升沿，**当`VALID`和`READY`同时为高时**，一次有效的数据传输（或地址传输）才会发生。

这个机制极其强大，它完美地解决了快慢设备之间的通信问题。快的设备可以等慢的设备`READY`，慢的设备也可以等快的设备`VALID`。

#### AXI4-Lite的五个“通信渠道”

AXI4-Lite把一次完整的读/写操作分成了几个独立的渠道，就像邮局把寄信和收信流程分开一样：

1.  **写地址渠道 (Write Address Channel - AW)**: 国王（Master）告诉邮局（Interconnect）要把信送到哪个地址（`AWADDR`）。
2.  **写数据渠道 (Write Data Channel - W)**: 国王把信的内容（`WDATA`）交给邮局。
3.  **写响应渠道 (Write Response Channel - B)**: 邮局告诉国王，信送到了（`BRESP`），任务完成。
4.  **读地址渠道 (Read Address Channel - AR)**: 国王告诉邮局，他想从哪个地址取信（`ARADDR`）。
5.  **读数据渠道 (Read Data Channel - R)**: 邮局把取回来的信的内容（`RDATA`）交给国王。

每个渠道都有自己的`VALID`/`READY`握手信号（例如`AWVALID`/`AWREADY`）。

**我们自己设计PWM模块时，只需要扮演“工匠”（Slave）的角色**，响应国王的指令。幸运的是，Vivado工具可以自动帮我们生成处理这些复杂握手逻辑的“秘书”，我们只需要和“秘书”对话就行了。

---

### 实战：引导你完成AXI + PWM的IP封装

现在，我们把上面的理论应用到你的`pwm.sv`上。

#### 核心思想转变

*   **旧思想**：`period`, `duty1`, `duty2`, `duty3` 是物理输入端口，由外部的GPIO硬连线驱动。
*   **新思想**：`period`, `duty1`, `duty2`, `duty3` 是**寄存器**，它们是存在于你的IP核内部的“邮箱”。CPU通过AXI总线，使用不同的地址来**写入**这些“邮箱”。

#### 步骤一：召唤“秘书”——使用IP Packager向导

1.  在Vivado中，`Tools -> Create and Package New IP` -> `Next`。
2.  选择 `Create a new AXI4 peripheral` -> `Next`。
3.  **信息填写**：
    *   Name: `pwm_axi` (或者你喜欢的名字)
    *   Version: `1.0`
    *   Display Name: `My PWM Controller with AXI`
4.  **接口配置 (最关键的一步)**：
    *   Interface Type: `AXI4-Lite` (我们的“短信服务”)
    *   Interface Mode: `Slave` (我们是“工匠”，被动接收指令)
    *   Data Width: `32` (AXI总线标准宽度)
    *   **Number of Registers**: 我们需要4个参数 (`period`, `duty1`, `duty2`, `duty3`)。所以这里填 **4**。这会自动为我们创建4个32位的可读写寄存器（“邮箱”）。
5.  点击`Next`，然后`Finish`。Vivado会为你创建一个全新的项目，里面包含了所有AXI接口的“秘书”代码。

#### 步骤二：介绍你的PWM给“秘书”

现在你进入了IP封装项目。

1.  **添加你的代码**：在Sources窗口中，右键 `Add Sources`，把你写好的 `pwm.sv` 文件添加进来。
2.  **打开“秘书”的办公室**：在Sources中，找到并打开顶层文件，名字通常是 `pwm_axi_v1_0_S00_AXI.v` 或 `.sv`。

#### 步骤三：连接PWM和“秘书”

在这个自动生成的顶层文件中，向下滚动，你会找到这样几行代码，它们就是“秘书”为你准备好的“邮箱”（寄存器）：

```systemverilog
// User logic registers
reg [C_S_AXI_DATA_WIDTH-1:0] slv_reg0;
reg [C_S_AXI_DATA_WIDTH-1:0] slv_reg1;
reg [C_S_AXI_DATA_WIDTH-1:0] slv_reg2;
reg [C_S_AXI_DATA_WIDTH-1:0] slv_reg3;
```
*   `slv_reg0` 就是地址 `0x00` 对应的邮箱。
*   `slv_reg1` 就是地址 `0x04` 对应的邮箱。
*   ...以此类推。

现在，我们要做的就是在文件的**末尾**（`endmodule`之前），把你的PWM模块例化出来，并把这些“邮箱”连接到它的输入端口。

```systemverilog
// 在 ..._S00_AXI.v 文件末尾添加以下代码

// 1. 例化你的PWM模块
pwm pwm_inst (
    // .clk(clk),  -> 应该连接到AXI总线的时钟
    .clk(S_AXI_ACLK),

    // .rst(rst), -> 应该连接到AXI总线的复位
    // 注意：AXI复位通常是低电平有效(ARESETN)，而你的模块可能是高电平有效。
    // 如果你的复位是高电平有效，需要加一个非门。
    .rst(!S_AXI_ARESETN), 

    // 2. 将寄存器（邮箱）连接到PWM的参数输入
    // 我们只关心寄存器的低16位，因为你的参数是16位的
    .period(slv_reg0[15:0]),
    .duty1 (slv_reg1[15:0]),
    .duty2 (slv_reg2[15:0]),
    .duty3 (slv_reg3[15:0]),

    // 3. 将PWM的输出引出到IP核的顶层
    .pwm1(pwm1_out),
    .pwm2(pwm2_out),
    .pwm3(pwm3_out)
);

// 4. 在文件顶部声明这些输出信号，让它们成为IP核的物理端口
// 找到模块的端口声明区域，添加以下几行：
/*
module pwm_axi_v1_0_S00_AXI # (...
)
(
    // ... 已有的AXI端口 ...
    output logic pwm1_out,
    output logic pwm2_out,
    output logic pwm3_out,
    // ...
);
*/

```

**画龙点睛（可选但强烈推荐）：实现状态回读**

AXI不仅能写，还能读。我们可以让CPU读回PWM内部的计数器值，这对于调试非常有用。

1.  **修改`pwm.sv`**: 给你的PWM模块增加一个输出端口 `output logic [15:0] counter_value`，并把内部的`counter`赋给它。
2.  **在`..._S00_AXI.v`中**：找到负责读操作的逻辑。通常会有一段 `case` 语句，根据读地址 `axi_araddr` 来选择输出哪个寄存器的数据。你可以在这里添加逻辑，当CPU读取某个特定地址（比如`slv_reg0`的地址）时，不返回`slv_reg0`的值，而是返回`counter_value`。
    ```systemverilog
    // 示例：
    always @( posedge S_AXI_ACLK )
    begin
      if (slv_reg_rden) // 如果有读请求
        begin
          case ( axi_araddr[ADDR_LSB+OPT_MEM_ADDR_BITS:ADDR_LSB] )
            2'h0: reg_data_out <= counter_value_from_pwm; // 读地址0时，返回计数器值
            2'h1: reg_data_out <= slv_reg1; // 读地址1时，返回duty1的设置值
            2'h2: reg_data_out <= slv_reg2;
            2'h3: reg_data_out <= slv_reg3;
            default : reg_data_out <= 0;
          endcase
        end
    end
    // 你需要先用线把pwm_inst的counter_value输出连接到一个叫counter_value_from_pwm的信号上。
    ```

#### 步骤四：打包和发布

1.  切换到 `Package IP` 标签页。
2.  检查 `File Groups`，确保你的`pwm.sv`已经被正确识别并添加。
3.  检查 `Customization Parameters` 和 `Ports and Interfaces`，确认一切无误。
4.  点击 `Review and Package` -> `Package IP`。

**恭喜！** 你已经成功地将一个纯逻辑模块，封装成了一个符合工业标准的、带AXI接口的、可复用的IP核。现在你可以回到你的主项目中，在IP Catalog里找到它，将它拖入Block Design，你会看到一个干净、专业的模块，它有一个AXI接口和三个PWM输出物理端口。