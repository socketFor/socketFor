---
title: "JFET"
date: "2026-08-02"
draft: false
tags: ["模电"]
---

# JFET

![image](/assets/image-20250816150457-7rvpjdq.png)

![image](/assets/image-20250816150702-f4lubc4.png)

Ugs加反压，耗尽层会变大，当反压足够大，会出现夹断的情况。

JFEC和耗尽型MOS有点像  

![image](/assets/image-20250816151214-22cdv0j.png)

‍

对比：JFET的输入电阻没有耗尽型MOS高，因为JFET的输入电阻是两个PN结的反偏之后等效出来的电阻，虽然说也很大，但是没有耗尽型MOS绝缘栅的输入电阻大。

耗尽型MOS比JEFT娇贵的多，因为二氧化硅绝缘层很薄容易被击穿。

‍

![image](/assets/image-20250816162721-lipkpci.png)

![image](/assets/image-20250816162812-duuhsk2.png)

I<sub>DSS：当Ugs固定以后，Uds增大出现预夹断，继续增大Uds后稳定的漏极到源极之间的电流，称之为饱和漏极电流。</sub>

![image](/assets/image-20250827232341-ixbg7o5.png)

‍

郭老师讲JFET

![image](/assets/image-20251003115840-r5m6tv9.png)

![image](/assets/image-20251003115931-34ed227.png)

JFET的控制电压U<sub>GS</sub>一定是加反压。

![image](/assets/image-20251003122938-y79l5lx.png)

JFET两种关断方法，一种是让U<sub>GS</sub>负压足够大，产生关门式关断。另一种是增大U<sub>DS</sub>沙漏式夹断。

当UGS没有达到关门式关断反压前提下且为常数的时候，我增大U<sub>DS</sub>本质上是改变U<sub>DG</sub>的电压，上面反型层到地的电压差最大，所以反型层的厚度最大，下面以此类推，产生反型层的不对称性。

‍

![image](/assets/image-20251003124329-khyyt28.png)

![image](/assets/image-20251003124557-32f6uyz.png)
