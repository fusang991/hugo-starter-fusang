---
title: "Zynq Xuartlite"
description: 
date: 2025-10-25T14:33:53+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: true
---

```c

//通过串口输入数字点亮对应的小灯
#include "xparameters.h"
#include "xuartlite.h"
#include "xuartlite_l.h"
#include "xil_io.h"
#include "xil_printf.h"
#include <xil_types.h>
#include "xgpio.h"
XUartLite puart_lite;
XGpio pl_sw;

void PL_SW_INIT();
u8 status;
u8 recv_package[8];
u8 send_package[8]={'i','n'};
u8 count;
int main()
{   

    PL_SW_INIT();
    XUartLite_Initialize(&puart_lite,XPAR_XUARTLITE_0_BASEADDR);
    
    XGpio_DiscreteSet(&pl_sw,2,0);
    XUartLite_Send(&puart_lite,send_package,2);
    while(1)
    
    {   
        count=0;
        XUartLite_Send(&puart_lite,send_package,2);
         //XUartLite_Recv(&puart_lite,recv_package,2);
         //while (count<2)
         //{
         //   if(count==0){XUartLite_Recv(&puart_lite,recv_package,2);}}
         //   if(count==1){XUartLite_Recv(&puart_lite,recv_package,1);}
         // XUartLite_Send(&puart_lite,recv_package,2);
                // 等待并接收第一个字节
        recv_package[0] = XUartLite_RecvByte(XPAR_XUARTLITE_0_BASEADDR);
        
        // 等待并接收第二个字节
        recv_package[1] = XUartLite_RecvByte(XPAR_XUARTLITE_0_BASEADDR);
         //XUartLite_Send(&puart_lite,recv_package,2);
       
         
         u32 led_control=10*(recv_package[0]-'0')+(recv_package[1]-'0');
        //xil_printf("%u",led_control);
        XGpio_DiscreteWrite(&pl_sw,2,0);
         XGpio_DiscreteWrite(&pl_sw,2,led_control);
         
        

        //XUartLite_Send(&puart_lite,recv_package,2);
        //XUartLite_Send(&puart_lite,send_package,2);
    }

}
void PL_SW_INIT()
{
    //初始化结构体
    XGpio_Initialize(&pl_sw, XPAR_AXI_GPIO_1_BASEADDR);
    //设置通道1，8位为输入模式
    XGpio_SetDataDirection(&pl_sw, 1,0xff);//1输入，0输出
    XGpio_SetDataDirection(&pl_sw, 2,0);
}
```