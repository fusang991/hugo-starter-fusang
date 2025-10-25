---
title: "Zynq Pwm"
description: 
date: 2025-10-23T21:46:18+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
tags:
    - zynq
    - digital
catagories:
    - zynq
---
# 优质blog
- [ustc:axi4总线](https://soc.ustc.edu.cn/CECS/lab5/axi/)

- [ustc:cecs2023 计算机综合实验](https://soc.ustc.edu.cn/CECS/)



#

状态设计之我见：
> 我从你的分析视角看到了我之前缺略的一个思路，就是很好的区分了边界和状态。简而言之，边界之间是状态。然而对于一般的设计，边界是易于表达的，而状态是易于理解的。以周期的计算来说，边界就是counter这个计数器构成了0，1，2---n-1这个n个边界点，其中存在突变点m。但是边界点却不能直观看出时间，我们将两个边界之间的点定义为状态。一般来说，每个状态的参数是相同的（比如每个状态的时间).然后我们就去计算我们要去讨论的大状态包含的基状态，比如一个周期包含的基状态，一个周期中上升沿包含的基状态。而基状态的研究只需要看大状态对应的边界点就行了   


# 代码

```verilog
//pwm.sv
// 1个端口控制输出pwm的周期，3个输入端口分别控制3个输出端口的占空比
module pwm (
    input logic clk,
    input logic [15:0] period,
    input logic [15:0] duty1,
    input logic [15:0] duty2,
    input logic [15:0] duty3,
    input logic rst,
    output logic pwm1,
    output logic pwm2,
    output logic pwm3
);

  logic [15:0] counter = 0;
  always_ff @(posedge clk, negedge rst) begin : pwm_block
    if (!rst) begin
      counter <= 0;
    end else begin
      if (counter < period - 1) counter <= counter + 1;
      else counter <= 0;
      //pmw1
    end
  end
  always_comb begin
    if (counter < duty1) begin
      pwm1 = 0;
    end else begin
      pwm1 = 1;
    end

    if (counter < duty2) begin
      pwm2 = 0;
    end else begin
      pwm2 = 1;
    end


    if (counter < duty3) begin
      pwm3 = 0;
    end else begin
      pwm3 = 1;
    end


  end
endmodule
// 注：这个代码写的并不规范，应该把组合逻辑部分放到时序部分变成同步更新，减少毛刺
```

```verilog
//tb_pwm.sv
module tb_pwm ();
  parameter real CLK_PERIOD = 10;  //假设时钟周期为10ns
  logic clk;
  logic [15:0] period;
  logic [15:0] duty1;
  logic [15:0] duty2;
  logic [15:0] duty3;
  logic rst;
  logic pwm1;
  logic pwm2;
  logic pwm3;
  pwm dut_pwm (
      .clk(clk),
      .period(period),
      .duty1(duty1),
      .duty2(duty2),
      .duty3(duty3),
      .rst(rst),
      .pwm1(pwm1),
      .pwm2(pwm2),
      .pwm3(pwm3)
  );

  initial begin
    clk = 0;
   end
  always begin
    #(CLK_PERIOD / 2) clk = ~clk;
  end

  initial begin
    rst = 1;
    @(posedge clk);
    period = 10;
    duty1 = 2;
    duty2 = 4;
    duty3 = 6;
    repeat (1000) @(posedge clk) ;
    $finish;
  end
endmodule

```


# PS 代码
```c
//ps 端通过uart控制占空比
#include "xparameters.h"
#include "xil_io.h"

#define period_offset 0x00
#define duty1_offset 0x04
#define duty2_offset 0x08
#define duty3_offset 0x12
int main(void){
   // T=period/fclk
   // 由于设计失误,duty1,2,3分别表示一个周期内低电平的状态数
   u32 period=1000;
   u32 duty1=1000;
   u32 duty2=2000;
   u32 duty3=3000;
   Xil_Out32(XPAR_PWM_IP_0_BASEADDR+period_offset,period);
   Xil_Out32(XPAR_PWM_IP_0_BASEADDR+duty1_offset,duty1);
   Xil_Out32(XPAR_PWM_IP_0_BASEADDR+duty2_offset,duty2);
   Xil_Out32(XPAR_PWM_IP_0_BASEADDR+duty3_offset,duty3);
   while(1)
   {

   }
}
```
# AXI