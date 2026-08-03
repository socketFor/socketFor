---
title: "JavaScript"
date: 2024-09-22
categories: 
  - "javascript"
tags: 
  - "javascript"
coverImage: "15.jpg"
---

### Document对象

[https://www.runoob.com/jsref/dom-obj-document.html](https://www.runoob.com/jsref/dom-obj-document.html)

### 表单提交

```
function login(event){
    event.preventDefault();//阻止提交
    var form = document.getElementById("loginform");//获取form
    var formdata = new FormData(form);//变成一个键值方便打包上传
    var data = formdata.entries();//数据在entries中
//遍历数组
    for(const itser of data){
        console.log(itser);
    }
}
```

### fetch请求

```
    var url='sight.php'//指定地址
    //fetch(url,body).then(response=>function(){}).cath(error=>捕获报错)
    fetch(url,{
        method : "post",
        body : formdata
    }).then(response =>console.log(response.json()))
    .catch(error =>{
        console.log(error)
    });
```

### 单页面导航栏内容切换

```
document.addEventListener('DOMContentLoaded',function(){
//监听等待DOM加载完成
    const navlisten = document.getElementsByClassName('nv');
//找nv类
    const valuelisten = document.getElementsByClassName('value');
//找value类
//循环遍历nv类并监听nv类中的点击事件
    for(i=0 ;i<navlisten.length;i++){
       const link =  navlisten[i];
       link.addEventListener('click',function(){
            const v_num = link.getAttribute('data-value-click');
//获取data-value-click中的data数据
            for(x=0;x<valuelisten.length;x++){
                const vlink = valuelisten[x];
                vlink.classList.remove('active');
            }
//列出每个value类中的所有类并从中移除active类
            const getvalueId = document.getElementById(v_num);
//获取v_num的id element
            getvalueId.classList.add('active');
//添加active类
       })
    }
})
```

### 时间函数-2s切换标题

```
<script>
    let time = setInterval(ChangeTitle,2000);
//创建计时事件2s执行函数
    let cg = true
    function ChangeTitle(){
        cg = !cg;
        let GetTitle = document.getElementById('ct');
        if(cg){
            GetTitle.text = '★☆美丽说，发现美★☆'
        }
        else{
            GetTitle.text = '☆★美丽说，分享美☆★'
        }
    }

</script>
```

### 更改css-:root中的变量实现彩色字

css

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
```

JavaScript

```
async function butiftext() {
    let root = document.documentElement;//获取根路径
    root.style.setProperty('--mian-h1-color', 'green');//修改:root中的变量值
    let bgColor = getComputedStyle(root).getPropertyValue('--mian-h1-color');//获取:root中的变量值
    console.log(bgColor);
```

### 监听窗口初始化

```
window.onload = function()
```

### 监听图片加载完成或者加载失败

```
            img.addEventListener('load',function haveload(){
                img.removeEventListener('load', haveload);
            })
            img.addEventListener('error',function erros(){
                img.src = 'img/404.png'
                img.removeEventListener('load',erros)
```

### 创建各种属性

```
    const a = document.createElement('div')
    const b = document.createElement('a')
    const c = document.createElement('img')
    a.className = 'pick-box'
    b.appendChild(c)
    a.appendChild(b)
    box.appendChild(a)
```

### 异步函数

```
async function(){
    await xxxx
}
```

### 异常检测

```
try {
    tryCode - 尝试执行代码块
}
catch(err) {
    catchCode - 捕获错误的代码块
}
finally {
    finallyCode - 无论 try / catch 结果如何都会执行的代码块
}
```

### 检测是否触底

```
window.addEventListener('scroll',()=>{
    const scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
    const clientHeight = document.documentElement.clientHeight || document.body.clientHeight;
    const scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
  
    if (scrollHeight - 10 <= scrollTop + clientHeight){

  }
```

### 检测是否存在某一个类

```
if(!(data.classList.contains("loadover"))){
     data.classList.add("loadover")
}
```

### 检测是否被点击

```
const Getlg = document.getElementsByClassName('lg');
const Getre = document.getElementsByClassName('re');

function Change_re_lg(){
    console.log('ok');

}

function re(){

}

function lg(){

}

Getlg[0].onclick = function(){
    Change_re_lg();
}
```

### 检测窗口滚动

```
window.addEventListener('scroll',function (){
    const header = this.document.querySelector('.navigation');
    if(window.scrollY>20)
        header.id = 'navbar';
    else
        header.id = '';
})
```
