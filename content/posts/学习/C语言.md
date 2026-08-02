---
title: "C语言"
date: "2026-08-02"
draft: false
tags: ["学习"]
---

# C语言

void serial_sendstring(char* string)

{

	unsigned char i;

	for(i = 0; String[i] ! = '\\0\'; i++)   字符串结束后会自动生成一个 '\0' 这样一个结束标志

	{

		Serial_SendByte(string[i]);

	}

}

因为我传入函数的内容是一个指向 char 型变量的指针，string 内存放的是一个地址，由于 c 语言中的字符串是由一个数组构成的，数组的内容是每一个字符，由于 Ascii 码表将 char 型数据转译为 字符，所以循环打印出来字符串数组中的内容就行。

调用函数：serial_sendstring("hellow world !") 括号内传入的是一段字符串，c 语言内部会将字符串转化为数组。 将数组的首地址传入函数，我需要打印出来数组中的每一个成员，由于传入函数的是数组的首地址，参数 string[i]的意思是 在首地址的基础上加上 i 个数据宽度。 也就是相当于用指针引用了数组中的内容。

‍

往一个数组里面写字符

例如 array[0] = 'A';

	array[1] = 'B';

	array[2] = 'C';

	最后别忘记在字符的最后加上array[4] = '\\0\';

我们可以通过OLED 或者printf 去显示字符串 printf（array（首地址））;

‍
