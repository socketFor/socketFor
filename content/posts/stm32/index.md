---
title: "Stm32"
date: 2025-02-22
categories: 
  - "单片机"
---

## 系统框架

[📥 下载 STM32固件库使用手册(中文版)](images/STM32固件库使用手册(中文版).pdf)

<!-- TODO: 图片缺失，原始路径 /tuku/STM32F103C8T6引脚定义.png -->
![](images/STM32F103C8T6%E5%BC%95%E8%84%9A%E5%AE%9A%E4%B9%89.png)

![](images/Snipaste_2025-02-22_13-01-50-1.png)

## 使用标注库

### 加入启动文件

![](images/1.png)

### 添加文件路径

![](images/2.png)

### 创建main函数

![](images/3.png)

![](images/4.png)

![](images/5.png)

### 添加标注库

![](images/6.png)

![](images/8.png)

## GPIO输入输出

![](images/9.png)

![](images/2-1.png)

![](images/10.png)

![](images/11.png)

## 按键消抖

![](images/Snipaste_2025-04-22_16-15-48.png)

## 中断

![](images/12.png)

![](images/1-1.png)

设置顺序为：**RCC-AFIO-EXTI-NVIC**

![](images/4-2.png)

![](images/3-2.png)

![](images/3-1.png)

![](images/4-1.png)

**每个中断函数都有固定的中断名称**

![](images/5-1.png)

**触发一个中断都会置为一个中断标志**

![](images/6-1.png)

![](images/7.png)

## 定时器

![](images/1-2.png)

RCCAPB1->选择内部时钟->时基单元->中断控制ITconfig->NVIC->运行控制cmd

![](images/1-3.png)

![](images/2-2.png)

T= 1/f

72MHz/7200=10kHz=10000Hz，去倒数得到时间(一次就是0.0001s)，记10000次，就是1s

![](images/5-2.png)

跟外部中断一样每个时间函数都有固定的名称

![](images/6-2.png)

![](images/8-1.png)

![](images/1-4.png)

## 比较模式（PWM）OC

![](images/1-5.png)

RCCAPB1->选择内部时钟->时基单元->CCR->GPIO->cmd启动

![](images/2-3.png)

![](images/3-4.png)

![](images/6-3.png)

**在线设置CCR的寄存值需要通过TIM\_SetCompare来修改**

![](images/7-1.png)

![](images/8-2.png)

![](images/2-5.png)

![](images/3-3.png)

![](images/10-1.png)

**正转由快到慢反转由慢到快**

![](images/11-1.png)

![](images/4-3.png)

![](images/2-4.png)

**GPIO口的输出模式要选择复用推挽输出模式**

## 引脚重映射

![](images/4-4.png)

![](images/5-3.png)

## 输入捕获IC

![](images/1-6.png)

GPIO->IC捕获单元->时基单元->从模式触发源选择->从模式设置->cmd启动

![](images/3-5.png)

![](images/4-5.png)

## 编码器

![](images/1-7.png)

GPIO->IC->encod编码器->时基单元->cmd启动

cnt计数由编码器控制不需要开启Tim3时钟及TIM\_InternalClockConfig

![](images/3-6.png)

![](images/2-6.png)

![](images/4-6.png)

![](images/5-4.png)

![](images/6-4.png)

## ADC数模转换

![](images/10-2.png)

![](images/11-2.png)

GPIO->ADC->ADC时钟分频->ADC通道选择和采样时间->ADCInit模式配置->ADCcmd

![](images/6-5.png)

![](images/12-1.png)

![](images/13.png)

![](images/14.png)

![](images/17.png)

![](images/15.png)

![](images/16.png)

![](images/7-2.png)

## DMA数据转运

![](images/1-8.png)

AHB->DMAInit->AMDcmd

![](images/2-7.png)

![](images/3-7.png)

### DMA和ADC多通道数据转运

![](images/1-9.png)

![](images/2-8.png)

## 通讯接口

[https://blog.csdn.net/qq\_52608074/article/details/122297014](https://blog.csdn.net/qq_52608074/article/details/122297014)

### 串口通讯

![](images/1-10.png)

![](images/2-9.png)

![](images/3-8.png)

![](images/4-8.png)

GPIO->USARTInit->CMD

![](images/5-6.png)

CRT流控

![](images/Snipaste_2025-04-15_14-06-06.png)

![](images/6-6.png)

重定义fputc函数->printf底层函数使其输出到tx端口

![](images/Snipaste_2025-04-15_14-27-58.png)

![](images/Snipaste_2025-04-15_14-27-19.png)

中文编译报错

![](images/Snipaste_2025-04-15_14-37-34.png)

![](images/Snipaste_2025-04-15_14-38-35.png)

```
--no-multibyte-chars
```

接受字符串（手动接收）

![](images/Snipaste_2025-04-15_15-10-56.png)

自动中断接收

![](images/Snipaste_2025-04-22_15-25-59.png)

接收包数据

![](images/Snipaste_2025-04-22_16-45-01.png)

## I2C

第一个传输数据要为接受方的id+一个读写位

![](images/Snipaste_2025-04-27_14-52-55.png)

![](images/Snipaste_2025-04-27_15-14-46.png)

![](images/Snipaste_2025-04-27_15-18-48.png)

![](images/Snipaste_2025-04-27_14-48-40.png)

第二个数据可以为指针位

![](images/Snipaste_2025-04-27_14-51-29.png)

![](images/Snipaste_2025-04-27_14-51-47.png)

![](images/Snipaste_2025-05-06_14-22-04.png)

![](images/Snipaste_2025-05-06_14-25-52.png)

![](images/Snipaste_2025-05-06_14-27-57.png)

![](images/Snipaste_2025-05-06_14-29-33.png)

![](images/Snipaste_2025-05-06_14-31-23.png)

![](images/Snipaste_2025-05-06_14-31-44.png)

![](images/Snipaste_2025-05-06_14-32-03.png)

### MPU6050

```
#ifndef __MPU6050_RAG_H
#define __MPU6050_RAG_H

#define	MPU6050_SMPLRT_DIV		0x19
#define	MPU6050_CONFIG			0x1A
#define	MPU6050_GYRO_CONFIG		0x1B
#define	MPU6050_ACCEL_CONFIG	0x1C

#define	MPU6050_ACCEL_XOUT_H	0x3B
#define	MPU6050_ACCEL_XOUT_L	0x3C
#define	MPU6050_ACCEL_YOUT_H	0x3D
#define	MPU6050_ACCEL_YOUT_L	0x3E
#define	MPU6050_ACCEL_ZOUT_H	0x3F
#define	MPU6050_ACCEL_ZOUT_L	0x40
#define	MPU6050_TEMP_OUT_H		0x41
#define	MPU6050_TEMP_OUT_L		0x42
#define	MPU6050_GYRO_XOUT_H		0x43
#define	MPU6050_GYRO_XOUT_L		0x44
#define	MPU6050_GYRO_YOUT_H		0x45
#define	MPU6050_GYRO_YOUT_L		0x46
#define	MPU6050_GYRO_ZOUT_H		0x47
#define	MPU6050_GYRO_ZOUT_L		0x48

#define	MPU6050_PWR_MGMT_1		0x6B
#define	MPU6050_PWR_MGMT_2		0x6C
#define	MPU6050_WHO_AM_I		0x75

#endif
```

| **寄存器地址​**​ | ​**​寄存器名称​**​ | ​**​关键参数​**​ | ​**​典型配置值​**​ | ​**​作用​**​ |
| --- | --- | --- | --- | --- |
| ​**​0x6B​**​ | PWR\_MGMT\_1 | `CLKSEL[2:0]`（时钟源）、`SLEEP`（休眠模式）、`DEVICE_RESET`（复位） | ​**​0x01​**​ | 解除休眠模式，选择X轴陀螺PLL时钟源 |

| ​**​0x19​**​ | SMPLRT\_DIV | 采样分频因子（取值范围0-255） | ​**​0x07​**​ | 采样率=1kHz/(1+7)=125Hz |
| --- | --- | --- | --- | --- |
| ​**​0x1A​**​ | CONFIG | `DLPF_CFG[2:0]`（数字低通滤波器带宽） | ​**​0x03​**​ | 设置低通滤波器带宽为5Hz，延迟19ms |
| ​**​0x1B​**​ | GYRO\_CONFIG | `FS_SEL[1:0]`（陀螺仪量程：00=±250°/s，10=±1000°/s） | ​**​0x10​**​ | 设置陀螺仪量程为±1000°/s（灵敏度32.8 LSB/°/s） |
| ​**​0x1C​**​ | ACCEL\_CONFIG | `AFS_SEL[1:0]`（加速度计量程：00=±2g，01=±4g） | ​**​0x08​**​ | 设置加速度计量程为±4g（灵敏度8192 LSB/g） |
| ​**​0x3B-0x48​**​ | 数据寄存器 | 加速度计（0x3B-0x40）、陀螺仪（0x43-0x48） | \- | 存储16位原始数据（高字节在前，低字节在后） |
| ​**​0x75​**​ | WHO\_AM\_I | 固定返回值`0x68` | \- | 验证设备连接状态 |

## SPI通信

[https://blog.csdn.net/qq\_52608074/article/details/122297014](https://blog.csdn.net/qq_52608074/article/details/122297014)

![](images/Snipaste_2025-06-27_16-31-24.png)

![](images/Snipaste_2025-06-27_16-31-10.png)

![](images/Snipaste_2025-06-27_17-34-14.png)

## CAN通讯

![](images/图片.png)

![](images/图片-1.png)

![](images/图片-2.png)

![](images/图片-3.png)

**0帧+ID+读写帧+（扩展格式）+发送数据长度+数据+CRC校验+有没有人接收**

![](images/图片-4.png)

![](images/图片-5.png)

![](images/图片-6.png)

![](images/图片-7.png)

![](images/图片-8.png)

![](images/图片-9.png)

## PWR-BKP-备份寄存器

备份寄存器由

Vbat引脚为备用电源维持备份区域供电

![](images/Snipaste_2025-06-27_18-46-53.png)

![](images/Snipaste_2025-06-27_18-51-09.png)

![](images/Snipaste_2025-06-27_18-51-34.png)

## RTC时钟

![](images/Snipaste_2025-06-27_19-03-33.png)

## 独立看门狗-窗口看门狗

![](images/Snipaste_2025-06-27_19-21-47.png)

![](images/Snipaste_2025-06-27_19-23-29.png)

![](images/Snipaste_2025-06-27_19-24-07.png)
