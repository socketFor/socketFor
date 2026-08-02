---
title: "BJT的特性曲线"
date: "2026-08-02"
draft: false
tags: ["模电"]
---

# BJT的特性曲线

![image](/assets/image-20250815144838-7nkm2rn.png) 

![image](/assets/image-20250815144850-rzdjmxa.png)

问题：**研究三极管的UBE和IB的关系的时候控制UCE不变 为什么UCE大于1V的时候UBE和IB的曲线不会移动了呢？**

![image](/assets/image-20250815150051-pvrlaru.png)

I<sub>C</sub>要结合U<sub>CE</sub>以及I<sub>B</sub>综合分析以后才能得出结果

1.3.4三极管的主要参数，自己去看

1.3.5温度对BJT输入的影响

![image](/assets/image-20250815160540-ftc5fss.png)

1.3.6温度对BJT输出的影响

![image](/assets/image-20250815161028-atdauoa.png)

I<sub>CEO</sub>穿透电流以及β放大倍数会受温度影响，二者随着温度升高都增大。

‍

![image](/assets/image-20250818103009-vwcdsk2.png)

Ic = V<sub>CC</sub> -U<sub>CE</sub> / R<sub>C</sub>

三极管的正偏电压是比反偏电压小很多的，当发射结和集电结都正偏的时候，此时U<sub>CE</sub>最小，当三极管工作在放大区的时候 Ic可以保持不变的原因是，即使你Vcc不断变大，我的Ic也不会跟着你V<sub>CC</sub>去走的原因是因为U<sub>CE</sub>在不断变大

‍
