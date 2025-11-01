---
title: "Spi Dac"
description: 
date: 2025-11-01T14:41:55+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
---

``` c
#include "xparameters.h"
#include "xspi.h"
#include "xspi_i.h"
#include "xspi_l.h"
#include "xil_io.h"
#include "sleep.h"

XSpi spi_dac;
u8 dac_send[2];
u8 dac_recv[2]={0};
// spi_dac:CPOL=1;CPHA=0
u32 dac_option=XSP_MASTER_OPTION|XSP_CLK_ACTIVE_LOW_OPTION|XSP_MANUAL_SSELECT_OPTION;
//u32 dac_option=XSP_MASTER_OPTION|XSP_CLK_ACTIVE_LOW_OPTION;
int main(void){
    XSpi_Initialize(&spi_dac, XPAR_DAC_SPI_BASEADDR);
    XSpi_SetOptions(&spi_dac, dac_option);
    XSpi_Start(&spi_dac);
    while(1){
        sleep(1);
        XSpi_SetSlaveSelect(&spi_dac,0x01);
        //XSpi_SetSlaveSelectReg(&spi_dac, 0x01);
        u16 dac_mode=(0x00);
        u16 dac_value=1000;
        u16 dac_data=dac_value|(dac_mode <<12);

        dac_send[0]=dac_data>>8;
        dac_send[1]=dac_data&0xff;
        XSpi_Transfer(&spi_dac, dac_send, dac_recv,2);
        XSpi_SetSlaveSelect(&spi_dac,0x00);
        //XSpi_SetSlaveSelectReg(&spi_dac, 0x00);
        sleep(1);
        XSpi_SetSlaveSelect(&spi_dac,0x01);
        //XSpi_SetSlaveSelectReg(&spi_dac, 0x01);
         dac_mode=(0x00);
         dac_value=0;
         dac_data=dac_value|(dac_mode <<12);

        dac_send[0]=dac_data>>8;
        dac_send[1]=dac_data&0xff;
        XSpi_Transfer(&spi_dac, dac_send, dac_recv,2);
        XSpi_SetSlaveSelect(&spi_dac,0x00);
        //XSpi_SetSlaveSelectReg(&spi_dac, 0x00);

    }

}
```