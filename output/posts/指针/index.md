---
title: "指针-数据结构"
date: 2025-03-23
categories: 
  - "单片机"
---

![](/wp-content/uploads/2025/03/2-1024x375.png)

![](/wp-content/uploads/2025/03/Snipaste_2025-06-28_14-51-40-1024x516.png)

&取地址 、（char\*,short\*,int\*,long\*）为数据类型指针、(\*p,\*a,...)为解引用指针-例如：\*p p为指针类型变量,\*p指读取存在指针类型变量里的地址，那个地址里的值。

![](/wp-content/uploads/2025/03/Snipaste_2025-04-29_16-10-47.png)

1bit=0xff=255

4个二进制=16位

8个二进制 = 32位

16个二进制=64位

![](/wp-content/uploads/2025/03/3-1024x338.png)

结构体指针用->指向成员变量=（\*结构体指针用）.指向成员变量

结构体变量用. 指向成员变量

![](/wp-content/uploads/2025/03/Snipaste_2025-06-28_14-58-41-1-1024x558.png)

![](/wp-content/uploads/2025/03/9-1024x480.png)

一个指针数组存放了每一个数组的首地址

![](/wp-content/uploads/2025/03/7-1024x350.png)

![](/wp-content/uploads/2025/03/Snipaste_2025-04-09_10-20-03-1024x506.png)

函数类型指针 void (\*p)(参数);

(void (\*)())0强制类型转换将0转成函数类型

(\*void(\*)()0)()在0处的函数类型指针

![](/wp-content/uploads/2025/03/Snipaste_2025-04-09_10-52-46-1024x525.png)

![](/wp-content/uploads/2025/03/Snipaste_2025-04-09_11-16-15-1024x365.png)

![](/wp-content/uploads/2025/03/Snipaste_2025-04-09_11-30-18-1024x432.png)

![](/wp-content/uploads/2025/03/Snipaste_2025-04-09_11-33-30.png)

![](/wp-content/uploads/2025/03/10.png)

## 链表

### 头插入式

```
#include <stdio.h>
#include <stdlib.h>

#define MaxArry 100

typedef int EM;

//创建结构体
typedef struct temp{
	temp* last;
	EM* data;
	temp* next;
	int lenght;
}Temp;

//初始化结构体
Temp* InitArry() {
	Temp* L = (Temp*)malloc(sizeof(Temp));	
	L->data = (EM*)malloc(sizeof(EM)*MaxArry+1);
	L->last = NULL;
	L->next = NULL;
	L->lenght = 0;
	return L;
}

//头插入式链表
void InsertChain(Temp* p,EM data) {
	Temp* L = (Temp*)malloc(sizeof(Temp));
	L->data = (EM*)malloc(sizeof(EM) * MaxArry + 1);
	L->data[0] = data;
	L->last = p;
	L->next = p->next;
	p->next = L;
}

//遍历链表
int ListArry(Temp* p) {
	Temp* nxt = p->next;
	while (nxt != NULL) {
		printf("%d\r\n", nxt->data[0]);
		nxt = nxt->next;
	}
	return 1;
}

int AddArry(Temp* p, EM data) {
	if (p->lenght >=MaxArry) return 0;
	p->data[p->lenght] = data;
	p->lenght++;
	return 1;
}

int InsertArry(Temp* p, int c, EM data) {
	if (c< 1 || p->lenght>=MaxArry||c>=MaxArry-1) return 0;
	for (int i = p->lenght-1; i >=c; i--) {
		p->data[i + 1] = p->data[i];
	}
	p->data[c] = data;
	p->lenght++;
	return 1;
}

int DeletArry(Temp* p, int c, EM* data) {
	if (c > MaxArry) return 0;
	*data = p->data[c - 1];
	for (int i = c - 1; i < p->lenght - 1; i++) {
		p->data[i] = p->data[i + 1];
	}
	p->lenght--;
	return 1;
}

int main(void)
{
	EM jicun;
	Temp* ary = InitArry();
	InsertChain(ary,10);
	InsertChain(ary, 20);
	InsertChain(ary, 30);
	InsertChain(ary, 40);
	ListArry(ary);
}

```

![](/wp-content/uploads/2025/03/Snipaste_2025-06-29_09-11-00-1024x393.png)

![](/wp-content/uploads/2025/03/Snipaste_2025-06-29_09-16-26-1024x525.png)

### 尾插入式

```
#include <stdio.h>
#include <stdlib.h>

#define MaxArry 100

typedef int EM;

//创建结构体
typedef struct temp{
	temp* last;
	EM* data;
	temp* next;
	int lenght;
}Temp;

//初始化结构体
Temp* InitArry() {
	Temp* L = (Temp*)malloc(sizeof(Temp));	
	L->data = (EM*)malloc(sizeof(EM)*MaxArry+1);
	L->last = NULL;
	L->next = NULL;
	L->lenght = 0;
	return L;
}

//头插入式链表
void InsertHead(Temp* p,EM data) {
	Temp* L = (Temp*)malloc(sizeof(Temp));
	L->data = (EM*)malloc(sizeof(EM) * MaxArry + 1);
	L->data[0] = data;
	L->lenght = 0;
	L->last = p;
	L->next = p->next;
	p->next = L;
}
//遍历链表
int ListArry(Temp* p) {
	Temp* nxt = p->next;
	while (nxt != NULL) {
		printf("%d\r\n", nxt->data[0]);
		nxt = nxt->next;
	}
	return 1;
}
//遍历返回尾节点
Temp* SerchTail(Temp* p) {
	Temp* L = p;
	while (L->next != NULL) {
		L = L->next;
	}
	return L;
}

//插入尾节点(p要为尾节点)
Temp* InsertTail(Temp* p, EM data) {
	Temp* L = (Temp*)malloc(sizeof(Temp));
	L->data = (EM*)malloc(sizeof(EM)*MaxArry);
	L->data[0] = data;
	L->lenght = 0;
	p->next = L;
	L->next = NULL;
	return L;
}

int main(void)
{
	EM jicun;
	Temp* ary = InitArry();
	Temp* tail = SerchTail(ary);
	tail = InsertTail(tail, 30);
	tail = InsertTail(tail, 40);
	tail = InsertTail(tail, 50);
	tail = InsertTail(tail, 60);
	ListArry(ary);
}

```

### 应用

#### 快速查询

![](/wp-content/uploads/2025/03/Snipaste_2025-06-29_14-39-55-1024x460.png)

```
#include<stdio.h>
#include<stdlib.h>
typedef int EM;
typedef struct Node {
	EM* data;
	Node* link;
}Node;

Node* InitChain() {
	Node* L = (Node*)malloc(sizeof(Node*));
	L->data = (EM*)malloc(sizeof(EM));
	L->link = NULL;
	return L;
}
//尾插式链
//p：为最后一个链
//data：传入数据
Node* InsertTail(Node* p, EM data) {
	Node* NewChian = (Node*)malloc(sizeof(Node*));
	NewChian->data = (EM*)malloc(sizeof(EM*));
	p->link = NewChian;
	*(NewChian->data) = data;
	NewChian->link = NULL;
	return NewChian;
}

//找尾链

Node* SerchTail(Node* p) {
	Node* tail = p;
	while (tail->link != NULL) {
		tail = tail->link;
	}
	return tail;
}

//查找倒数K个值中的链data值

EM SerchData(Node* p, int k) {
	Node* S = p->link;
	int cunt = 0;
	while (S != NULL) {
		cunt++;
		S = S->link;
	}
	S = p->link;
	while (cunt > k) {
		cunt--;
		S = S->link;
	}
	return S->data[0];
}

//快慢指针
EM SerchData2(Node* p,int data) {
	Node* fast = p->link;
	Node* slow = p->link;
	for (int i = 0; i < data; i++) {
		fast = fast->link;
	}
	while (fast != NULL) {
		fast = fast->link;
		slow = slow->link;
	}
	return slow->data[0];
}

//遍历链
int ListChian(Node* p) {
	Node* L = p->link;
	while (L != NULL) {
		printf("%d\r\n", L->data[0]);
		L = L->link;
	}
	return 1;
}

int main2(void) {
	
	Node* ary = InitChain();
	Node* tail = SerchTail(ary);
	tail = InsertTail(tail, 30);
	tail = InsertTail(tail, 50);
	tail = InsertTail(tail, 60);
	tail = InsertTail(tail, 70);
	tail = InsertTail(tail, 80);
	tail = InsertTail(tail, 90);
	tail = InsertTail(tail, 100);
	ListChian(ary);
	printf("那个数是:%d\r\n",SerchData2(ary, 3));
	return 0;
}
```

![](/wp-content/uploads/2025/03/Snipaste_2025-06-29_14-42-12-1024x432.png)

```
#include<stdio.h>
#include<stdlib.h>
typedef char EM;
typedef struct Node {
	EM* data;
	Node* link;
}Node;

static Node* InitChain() {
	Node* L = (Node*)malloc(sizeof(Node*));
	L->data = (EM*)malloc(sizeof(EM));
	L->link = NULL;
	return L;
}
//尾插式链
//p：为最后一个链
//data：传入数据
static Node* InsertTail(Node* p, EM data) {
	Node* NewChian = (Node*)malloc(sizeof(Node*));
	NewChian->data = (EM*)malloc(sizeof(EM*));
	p->link = NewChian;
	*(NewChian->data) = data;
	NewChian->link = NULL;
	return NewChian;
}

//找尾链

static Node* SerchTail(Node* p) {
	Node* tail = p;
	while (tail->link != NULL) {
		tail = tail->link;
	}
	return tail;
}

//数个数
static int CuntChain(Node* p) {
	Node* i = p->link;
	int cunt = 0;
	while (i != NULL) {
		cunt++;
		i = i->link;
	}
	return cunt;
}

//找尾缀相同链
static EM SerchAsChain(Node* p, Node* k) {
	Node* fast = p->link;
	Node* slow = k->link;
	int inum  = CuntChain(p);
	int knum = CuntChain(k);
	int less = abs(inum - knum);
	for (int i = 0; i < less; i++) {
		fast = fast->link;
	}
	while (fast != slow) {
		fast = fast->link;
		slow = slow->link;
	}
	return slow->data[0];
}

//遍历链
static int ListChian(Node* p) {
	Node* L = p->link;
	while (L != NULL) {
		printf("%c\r\n", L->data[0]);
		L = L->link;
	}
	return 1;
}

int main(void) {

	Node* ary = InitChain();
	Node* ary1 = InitChain();
	Node* last = InitChain();

	Node* ttl = SerchTail(last);
	ttl = InsertTail(ttl, 'i');
	ttl = InsertTail(ttl, 'n');
	ttl = InsertTail(ttl, 'g');

	Node* tail = SerchTail(ary);
	tail = InsertTail(tail, 'l');
	tail = InsertTail(tail, 'o');
	tail = InsertTail(tail, 'a');
	tail = InsertTail(tail, 'd');
	tail->link = last ->link;
	tail = SerchTail(ary1);
	tail = InsertTail(tail, 'b');
	tail = InsertTail(tail, 'e');
	tail->link = last->link;

	ListChian(ary);
	ListChian(ary1);
	printf("那个数是:%c\r\n", SerchAsChain(ary, ary1));

	return 0;
}
```
