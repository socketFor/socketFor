---
title: "CSS"
date: 2024-09-22
categories: 
  - "css"
tags: 
  - "css"
---

参考：[https://man.ilovefishc.com/css3/](https://man.ilovefishc.com/css3/)

工具：[https://css.bqrdh.com/glassmorphism-editor](https://css.bqrdh.com/glassmorphism-editor)

### 居中布局

```
//绝对定位布局:
position: absolute;
top: 50%;
left: 50%;
transform: translate(-50%,-50%);
//父元素flex布局
display: flex;
justify-content: center;
align-items: center;
```

### li标签去除圆点

```
list-style: none;
```

### a标签去除下划线

```
text-decoration: none;
```

### 元素靠右

```
margin-left: auto;
```

### flex-横向对齐

```
display: flex;
align-items: center;
```

### flex-纵向对齐

```
display: flex;
justify-content: center;
```

### 去除input点击后的边框

```
input:focus{
    outline: none;
    background-color:rgb(227, 229, 231) ;
}
```

### 去除html边框

```
*{
    padding: 0 auto;
    margin: 0 auto;
}
```

### Grid布局

```
 display: grid;  
 grid-template-columns: repeat(auto-fill,minmax(300px,1fr));
//横排重复填充填充次数为自动，最小横排大小为300px，最大1fr占满
 /* 设置横排的格式 */
 gap: 20px;
//间距20px
```

https://www.bilibili.com/video/BV1gw41167gh/?spm\_id\_from=333.337.search-card.all.click&vd\_source=c8acca57af0b5c7e066a91f435bcfdb7

### 设置弹窗脱离文本流(导航栏置顶)

```
 position: fixed;
```

### 文本超出自动影藏

overflow: hidden;

### 监听窗口大小

```
@media only screen and (max-width: 600px){
     csscode;
}
//放在css最后放前面会被替换
```

### 设置公共参数

```
:root{
    /* 渐变字 */
    --mian-h1-color:red;
}

body{
    text-align: center;

}
.butifultext{
    color:var(--mian-h1-color);
    transition: color 2s;
}
//可以通过js获取值并更改
```

### 动画时间设置

```
transition: 1.5s;
```

### 隐藏页面但占据空间

```
visibility: hidden;
```

### 垂直排列

```
flex-direction: column;
```

### 设置透明度

```
opacity: 0.4;
```

### 在绝对定位下设置页面优先级

```
z-index: 100;
```

### 设置鼠标样式

```
cursor: pointer;
```

### 计算函数

```
calc(100% - 500px);注意减号前后要加空格
```
