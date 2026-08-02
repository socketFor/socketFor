---
title: "增强型MOSFET"
date: "2026-08-02"
draft: false
tags: ["模电"]
---

# 增强型MOSFET

1.1N 沟道增强型 MOSFET

![image](/assets/image-20250816124716-vx324ws.png)

![image](/assets/image-20250816125013-q6vsi43.png)

栅极 g 跟谁都将绝缘   栅极是控制极，类似 BJT 的 B（基极）

![image](/assets/image-20250816130230-i7sv9ed.png)

在 Uds = 0 的前提下，s 是和 P 连在一起的，g 和 s 又是连在一起。如果这时候在 gs 之间加电压相当于，在 gP 之间加了电压电场向下，耗尽层的空穴往下移动，自由电子向上，Ugp 电压越大，N 沟道越宽。N 沟道越宽电阻越小，得到了一个 Ugs 电压控制的一个可变电阻器。

‍

‍

![image](/assets/image-20250816131923-k9764tc.png)

可以用等势面的观点看：当 uds=0 时，s 和 d 的电势相等都为 0，此时 Udg 和 Ugs 二者的电势差是一样的，当 d 处增加电压，d 电势增大，g 和 d 之间的电势差减小，右侧必然向电势差大的方向移动，左侧无影响故右侧向左上移动，但是不是完全封闭。

![image](/assets/image-20251001142411-izbzihv.png)

‍

![image](/assets/image-20250816140318-er4q2ks.png)

在 U<sub>gs</sub>＞U<sub>gs(th)</sub>（开启沟道的最低阈值电压）后，I<sub>ds</sub> 与 U<sub>ds</sub> 符合如图曲线规律，当 U<sub>gs</sub> - U<sub>ds</sub> = U<sub>gs(th)</sub>的时候是预夹断，并不是真的完全夹断还有一个缝隙，夹断之前 N 沟道的电阻近似不变与 U<sub>GS</sub> 正相关。夹断之后 I<sub>ds</sub> 大小不变，U<sub>ds</sub> 不断增大，电阻也随着我缝隙越来越长电阻越来越大，刚好近似抵消，I<sub>ds</sub> 不变，出现米勒平台。

‍

插入：寄生二极管的由来

![image](/assets/image-20251001140743-u4uita6.png)

想到了一个问题：U<sub>DS</sub>太大会让mos管被击穿，我在想是不是U<sub>DS</sub>太大让整个沟道都被夹断以后mos管就毁了？，那我让U<sub>GS</sub>变大不让他完全夹断，是不是就可以提高U<sub>DS</sub>的击穿电压呢？

‍

答：不是。UDS 增大导致的夹断与击穿是两个不同过程：夹断是沟道在漏端被耗尽区 "截断"，此时仍可导电（饱和区）；而击穿是漏端耗尽区电场过强导致载流子雪崩倍增，属于破坏性失效。

UGS 增大虽能延缓沟道夹断，但会使漏端耗尽区更集中、电场更强，反而降低击穿电压。因此，增大 UGS 不能提高击穿电压，反而会使其降低。

‍

总结：预夹断点之前，R<sub>ds</sub> 的阻值大小不变=不同的 U<sub>gs</sub> 对应不同的 R<sub>ds</sub>，此时 I<sub>ds</sub> 和 U<sub>ds</sub> 成正比。

	   预夹断点之后，在 U<sub>gs</sub> 不变的前提下 R<sub>ds</sub> 随着 U<sub>ds</sub> 的增大而增大，I<sub>ds</sub> 不变，因此 U<sub>ds</sub> 不对 I<sub>ds</sub> 起作用，I 完 全由 U<sub>gs</sub> 决定。

‍

为什么叫增强型？因为在 U<sub>gs</sub>＞U<sub>gs(th)</sub>（开启沟道的最低阈值电压）后才有后续的故事，所以要对 U<sub>gs</sub> 增强才行。耗尽型就是天生有沟道（在二氧化硅里面补充大量正电荷）

耗尽型有一个关断的反向电压 U<sub>gs off</sub> 天生有沟道不代表不能关 Ugs 加反向电压就可以关。 

‍

‍

‍

1.2 场效应管的特性曲线和主要参数

‍

![image](/assets/image-20250816152818-8blfqyf.png)

![image](/assets/image-20250827232341-ixbg7o5.png)

a 图工作在恒流区，是 MOS 管的转移特性。转移特性描述的是 U<sub>gs</sub> 对 I<sub>d</sub> 的控制，前提是 uds 要足够大，会有预夹断的不变电流，只受 ugs 变化而变化。

‍

要设计增强型 mos 管的放大电路要利用转移特性

MOS管的转移特性：

![image](/assets/image-20251001143316-3m13das.png)

描述：当U<sub>DS</sub>大于夹断电压以后，U<sub>GS</sub>对I<sub>D</sub>的控制就叫做转移特性

1.3 主要参数

![image](/assets/image-20250816165300-yz77648.png)

作业 1.3.4 晶体管的主要参数

作业 1.3.5 温度对晶体管的主要参数的影响

作业  1.3.6 光电三极管

‍

一点点补充：

中间那个箭头其实也是一个PN结，衬底如果是P型的，就是衬底指向反型层，形成N沟道。如果衬底是N型的，反省层就是P型指向衬底，形成P沟道。

![image](/assets/image-20251001203435-zq3bdrf.png)

![image](/assets/image-20251001203810-2evofu5.png)

![image](/assets/image-20251001204712-n5mars3.png)

夹断过程：耗尽层膨胀。

![image](/assets/image-20251001205403-5lzq967.png)

耗尽层的本质：PN结，P接正，N结负，电流从P流向N。P中载流子是空穴，N中载流子是自由电子，电子从N流向P，P在间隙左右带负电，N在间隙左右带正电。-------------中间有电场但是保留电子，耗尽层可以导电。

‍

MOS管的厂商：![image](/assets/image-20251001222215-jfxpsiu.png)

一般MOS封装小，功率就小，开启电压就会小。
