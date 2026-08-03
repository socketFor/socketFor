---
title: "JavaScript-canva属性"
date: 2024-12-08
categories: 
  - "javascript"
coverImage: "back.jpg"
---

[https://www.w3school.com.cn/tags/html\_ref\_canvas.asp](https://www.w3school.com.cn/tags/html_ref_canvas.asp)

### 循环画圆程序

```
function sleep(time){
    return new Promise(function(resolve){
        setTimeout(resolve,time)
    })
    //延迟异步函数
}

async function drow() {
    let ctx = document.getElementById('drows').getContext('2d')
    ctx.clearRect(0, 0, 300, 300);
    //清除画布
    for(var i=0;i<=10;++i){
        ctx.beginPath();
        //方法开始一条路径，或重置当前的路径。
        ctx.arc(150,150,150,0*Math.PI,0.2*i*Math.PI)
        //画圆
        ctx.stroke();
        //方法会实际地绘制出通过 moveTo() 和 lineTo() 方法定义的路径。默认颜色是黑色。
        await sleep(90);
        //延迟
    }
    window.requestAnimationFrame(drow);
    //动画
}
function init(){
    window.requestAnimationFrame(drow);

}

window.onload = init
```

其中的sleep函数解释

```
const myFirstPromise = new Promise((resolve, reject) => {
  // 当异步操作成功时，我们调用 resolve(...)，当其失败时，调用 reject(...)。
  // 在这个例子中，我们使用 setTimeout(...) 来模拟异步代码。
  // 在实际情况中，你可能会使用类似 XHR 或 HTML API 等。
  setTimeout(() => {
    resolve("成功！"); // 耶！一切顺利！
  }, 250);
});

myFirstPromise.then((successMessage) => {
  // successMessage 是我们在上面的 resolve(...) 函数中传入的任何内容。
  // 它不一定是字符串，但如果它只是一个成功的消息，那么它大概率是字符串。
  console.log(`耶！${successMessage}`);
});
```
