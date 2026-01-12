---
title: "Zynq Spi"
description: 
date: 2025-11-08T17:10:57+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
    - other
---

```c
#include "xil_printf.h"
#include "xparameters.h"
#include "xparameters_ps.h"
#include "xspips.h"
#include "xspips_hw.h"
#include "sleep.h"

#define prescaler 16
#define data_length 16
#define data_count data_length/8


int main(void){
    xil_printf("0");
XSpiPs_Config*  spi_config=XSpiPs_LookupConfig(XPAR_SPI0_BASEADDR);
XSpiPs spi;
XSpiPs_CfgInitialize(&spi,spi_config,XPAR_SPI0_BASEADDR);

u32 options=XSPIPS_MASTER_OPTION|XSPIPS_CLK_ACTIVE_LOW_OPTION|XSPIPS_CLK_PHASE_1_OPTION;
XSpiPs_SetOptions(&spi,options);
xil_printf("a");
XSpiPs_SetClkPrescaler(&spi,XSPIPS_CLK_PRESCALE_16);
xil_printf("b");
//dac data
u16 mode1=0b1100;
u16 data1=1024;
// high voltage
u16 send1=(mode1<<12)|(data1);
u8 send1_buf[2];
send1_buf[0]=send1>>8;
send1_buf[1]=send1&0xff;
u8 sleep_time=1;
// low voltage
u16 data2=0;
u16 send2=(mode1<<12)|(data2);
u8 send2_buf[2];
send2_buf[0]=send2>>8;
send2_buf[1]=send2&0xff;
XSpiPs_Enable(&spi);
XSpiPs_SetSlaveSelect(&spi,0x00);
xil_printf("1");
while(1){
    //sleep(1);
    sleep(sleep_time);
    //XSpiPs_SetSlaveSelect(&spi,0x01);
    XSpiPs_Enable(&spi);
    XSpiPs_PolledTransfer(&spi, send1_buf, NULL, 2);
   // xil_printf("2");
   // XSpiPs_Transfer(&spi, send1_buf, NULL, 2);
    //sleep(1);
    sleep(sleep_time);
    XSpiPs_Enable(&spi);
   // XSpiPs_SetSlaveSelect(&spi,0x01);
    XSpiPs_PolledTransfer(&spi, send2_buf, NULL, 2);
    //xil_printf("3");
    //XSpiPs_Transfer(&spi, send2_buf, NULL, 2);
}

}
```