---
title: "Zynq Gpio"
description: 
date: 2025-11-01T15:51:42+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
    - other
---

``` c
#include "xil_io.h"
#include "xparameters.h"
#include "xparameters_ps.h"
#include "xil_printf.h"
#include "xgpio.h"
#include "xgpiops.h"
#include "xuartps.h"
#include "xuartps_hw.h"
void PL_SW_INIT();
void PS_GPIO_INIT();
void delay_1s();
void led_on_off(u32 on);
void uart_init();
XGpio pl_sw;
XGpioPs ps_gpio;
XGpioPs_Config ps_gpio_config;
XUartPs uart;
XUartPs_Config uart_config;
void main()
{
    print("hello world\n");
    PL_SW_INIT();
    PS_GPIO_INIT();
    u32 pin12,pin13,pin14,pin15,pin47;
    u32 sw_cur,sw_last=0;
    u32 pl_sw_state;
   XGpio_DiscreteSet(&pl_sw,2,0xff);
    while(1)
    {  
      /*  led_on_off(1);
        delay_1s();
        led_on_off(0);
        delay_1s();*/
        pl_sw_state=XGpio_DiscreteRead(&pl_sw,1);
        XGpio_DiscreteWrite(&pl_sw,2,pl_sw_state);
        pin12=XGpioPs_ReadPin(&ps_gpio, 12);
        pin13=XGpioPs_ReadPin(&ps_gpio, 13);
        pin14=XGpioPs_ReadPin(&ps_gpio, 14);
        pin15=XGpioPs_ReadPin(&ps_gpio, 15);
        pin47=XGpioPs_ReadPin(&ps_gpio, 47);
        XGpioPs_WritePin(&ps_gpio, 7, pin47);
        XGpioPs_WritePin(&ps_gpio, 8, pin12);
        XGpioPs_WritePin(&ps_gpio, 9, pin14);
        XGpioPs_WritePin(&ps_gpio, 10, pin15);
        XGpioPs_WritePin(&ps_gpio, 11, pin13);
        sw_cur=XGpio_DiscreteRead(&pl_sw, 1);
       /* if(sw_cur&0x1==0x1)
        {
            led_on_off(1);
        }
        else {
            led_on_off(0);
        }*/
        if(sw_cur!=sw_last)
        {
            xil_printf("pl key is %x\n",sw_cur);
            sw_last=sw_cur;
        }
                
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

void PS_GPIO_INIT()
{
    XGpioPs_CfgInitialize(&ps_gpio, &ps_gpio_config,XPAR_XGPIOPS_0_BASEADDR);
    XGpioPs_SetDirectionPin(&ps_gpio, 12, 0);//0输入，1输出
    XGpioPs_SetDirectionPin(&ps_gpio, 13, 0);
    XGpioPs_SetDirectionPin(&ps_gpio, 14, 0);
    XGpioPs_SetDirectionPin(&ps_gpio, 15, 0);
    XGpioPs_SetDirectionPin(&ps_gpio, 47, 0);

    XGpioPs_SetDirectionPin(&ps_gpio, 7, 1);
    XGpioPs_SetOutputEnablePin(&ps_gpio,7,1);
    XGpioPs_SetDirectionPin(&ps_gpio, 8, 1);
    XGpioPs_SetOutputEnablePin(&ps_gpio,8,1);
    XGpioPs_SetDirectionPin(&ps_gpio, 9, 1);
    XGpioPs_SetOutputEnablePin(&ps_gpio,9,1);
    XGpioPs_SetDirectionPin(&ps_gpio, 10, 1);
    XGpioPs_SetOutputEnablePin(&ps_gpio,10,1);
    XGpioPs_SetDirectionPin(&ps_gpio, 11, 1);
    XGpioPs_SetOutputEnablePin(&ps_gpio,11,1);

}
void delay_1s()
{
    for(int i=0;i<1000000;i++)
    for(int j=0;j<100;j++);
}

void led_on_off(u32 on)
{
    
    XGpioPs_WritePin(&ps_gpio, 7, on);
    XGpioPs_WritePin(&ps_gpio, 8, on);
    XGpioPs_WritePin(&ps_gpio, 9, on);
    XGpioPs_WritePin(&ps_gpio, 10, on);
    XGpioPs_WritePin(&ps_gpio, 11, on);
}

void uart_init()
{
    XUartPs_CfgInitialize(&uart,&uart_config,XPS_UART1_BASEADDR);
    XUartPs_SetBaudRate(&uart,115200);
}
```