---
title: "脉宽调制——正弦波"
date: 2025-06-05
categories: 
  - "单片机"
---

## 实现方法：

通过不同占空比的方波信号经过**RC低通滤波**实现正弦波效果

![](/wp-content/uploads/2025/06/Snipaste_2025-06-05_13-38-03.png)

由generate\_sine\_pwm\_values函数产生所需占空比数据

```

/**
 * @brief 生成正弦波对应的PWM占空比序列
 * @param arr 自动重装载值（决定PWM周期）
 * @param amplitude 正弦波幅度系数（0.0~0.5，建议0.3）
 * @param ccr_values 输出数组，存储计算后的CCR寄存器值
 * @param n 采样点数（默认100）
 * @note 占空比范围：0.5±amplitude（避免0%/100%失真）
 */
void generate_sine_pwm_values(uint16_t arr, float amplitude, uint16_t *ccr_values, uint16_t n) {
    for (uint16_t i = 0; i < n; i++) {
        // 计算正弦波角度（0~2π）
        float angle = 2 * M_PI * i / n;
        
        // 计算占空比：0.5 + 幅度*sin(角度)
        float duty_cycle = 0.5f + amplitude * sinf(angle);
        
        // 映射到CCR寄存器值（占空比 * ARR）
        ccr_values[i] = (uint16_t)(duty_cycle * arr);
    }
}
```

## 完整代码：

```
#include "stm32f10x.h"                  // Device header
#include "math.h"
#include <stdint.h>
#define GA GPIOA
#define GP GPIO_Pin_0
#ifndef M_PI
#define M_PI 3.14159265358979323846
#endif
#define pfreq 20000   //实际需要正弦的频率
#define SINE_POINTS 25  // 正 弦波采样点数
#define pp pfreq*SINE_POINTS  //实际需要的频率
#define psc 1      //预分频系数，低的话分辨率高

// 正弦波查找表
uint16_t sineTable[SINE_POINTS];
unsigned char status = 0;
uint32_t ARRPeriod   = (72000000 / (pp*psc)) - 1; //需要频率的arr值

/**
 * @brief 生成正弦波对应的PWM占空比序列
 * @param arr 自动重装载值（决定PWM周期）
 * @param amplitude 正弦波幅度系数（0.0~0.5，建议0.3）
 * @param ccr_values 输出数组，存储计算后的CCR寄存器值
 * @param n 采样点数（默认100）
 * @note 占空比范围：0.5±amplitude（避免0%/100%失真）
 */
void generate_sine_pwm_values(uint16_t arr, float amplitude, uint16_t *ccr_values, uint16_t n) {
    for (uint16_t i = 0; i < n; i++) {
        // 计算正弦波角度（0~2π）
        float angle = 2 * M_PI * i / n;
        
        // 计算占空比：0.5 + 幅度*sin(角度)
        float duty_cycle = 0.5f + amplitude * sinf(angle);
        
        // 映射到CCR寄存器值（占空比 * ARR）
        ccr_values[i] = (uint16_t)(duty_cycle * arr);
    }
}

void PwmInit(void){

	generate_sine_pwm_values(ARRPeriod,0.3,sineTable,SINE_POINTS);
	
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM2,ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	

	TIM_TimeBaseInitTypeDef  TIM_TimeBaseInitStruct;

	TIM_TimeBaseInitStruct.TIM_ClockDivision = TIM_CKD_DIV1;
	TIM_TimeBaseInitStruct.TIM_CounterMode = TIM_CounterMode_Up;
	TIM_TimeBaseInitStruct.TIM_Period =ARRPeriod;
	TIM_TimeBaseInitStruct.TIM_Prescaler = psc-1;
	TIM_TimeBaseInitStruct.TIM_RepetitionCounter = 0;
	TIM_TimeBaseInit(TIM2,&TIM_TimeBaseInitStruct);
	
	TIM_ITConfig(TIM2, TIM_IT_Update, ENABLE);			

	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);				
	NVIC_InitTypeDef NVIC_InitStructure;			
	NVIC_InitStructure.NVIC_IRQChannel = TIM2_IRQn;				
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;			
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 2;	
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;			
	NVIC_Init(&NVIC_InitStructure);							

	GPIO_InitTypeDef GPIO_InitStruct;
	GPIO_InitStruct.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStruct.GPIO_Pin = GPIO_Pin_1;
	GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GA,&GPIO_InitStruct);
//	GPIO
	
	TIM_OCInitTypeDef TIM_OCInitStruct;
	TIM_OCStructInit(&TIM_OCInitStruct);
	TIM_OCInitStruct.TIM_OCMode = TIM_OCMode_PWM1;
	TIM_OCInitStruct.TIM_OCPolarity = TIM_OCPolarity_High;
	TIM_OCInitStruct.TIM_OutputState = TIM_OutputState_Enable;
	TIM_OCInitStruct.TIM_Pulse = 0;
	TIM_OC2Init(TIM2,&TIM_OCInitStruct);

	TIM_Cmd(TIM2,ENABLE);

	
}

volatile uint16_t sineIndex = 0;
void TIM2_IRQHandler(void)
{
    if (TIM_GetITStatus(TIM2, TIM_IT_Update) != RESET)
    {
        // 更新PWM占空比
			uint8_t hol = 0;
			TIM_SetCompare2(TIM2, sineTable[sineIndex]);
			sineIndex++;
      if (sineIndex >= SINE_POINTS)
       {
					sineIndex=0;
			 }
        // 清除中断标志位
        TIM_ClearITPendingBit(TIM2, TIM_IT_Update);
    }
}
```
