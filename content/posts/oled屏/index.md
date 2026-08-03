---
title: "OLED屏"
date: 2025-07-11
categories: 
  - "单片机"
---

OLED硬件滚动

```
void OLED_START_ROLLER(void)
{
	OLED_WriteCommand(0x2E); //关闭滚动
	OLED_WriteCommand(0x27); //0x26向左滚动，0x27向右滚动
	OLED_WriteCommand(0x00); //固定0x00
	OLED_WriteCommand(0x03); //起始滚动页
	OLED_WriteCommand(0x07); //滚动帧率（0x00最快，0x07最慢）
	OLED_WriteCommand(0x07); //结束页（需≥起始页）
	OLED_WriteCommand(0x00); //虚拟字节
	OLED_WriteCommand(0xFF); // 虚拟字节（固定0xFF）
	OLED_WriteCommand(0x2F); //开始滚动
}
```
