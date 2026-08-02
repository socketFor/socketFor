---
title: "温度上升会导致静态工作点Q的变换"
date: "2026-08-02"
draft: false
tags: ["模电"]
---

# 温度上升会导致静态工作点Q的变换

1.温度上升会导致静态工作点Q的变换

![image](/assets/image-20250824152902-bzot7ge.png)

![image](/assets/image-20250824153033-h033dh4.png)

![image](/assets/image-20250824153046-3cq5h7m.png)

1. **温度升高**：易使 Q 点上移（集电极电流 IC 增大），若原 Q 点偏高，可能先引发**饱和失真**；但若电路偏置不稳定（如固定偏置电路），温度骤升后若 IC 异常增大，反向偏置不足，也可能间接因 Q 点偏移导致截止失真（较少见，多为饱和失真）。
2. **温度下降**：会使 IC 减小，Q 点下移。若原 Q 点偏低，IC 进一步减小会导致发射结正向偏置不足，输入信号负半周时三极管提前截止，引发**截止失真**（更典型）。

![image](/assets/image-20250824164336-hmrdfvv.png)

![image](/assets/image-20250824164702-dxxylv3.png)

因为UBE = VB -VE   为了让UBE减小 所以我们要让VB保持不变，条件1就是让VB保持不变的方法。

![image](/assets/image-20250824170024-c19grrw.png)

![image](/assets/image-20250824171320-uy80frm.png)

为了稳定Q点

![image](/assets/image-20250825141219-r0wgqv2.png)

![微信图片_2025-08-25_120813_192](/assets/微信图片_2025-08-25_120813_192-20250825140915-nq9bhpt.png)

‍

‍

其他静态工作点稳定的电路

![image](/assets/image-20250825141813-ea2p2mo.png)

自己去看。
