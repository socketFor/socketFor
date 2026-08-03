---
title: "Python-Opencv库"
date: 2024-12-03
categories: 
  - "python"
coverImage: "17.png"
---

```
import cv2 as cv
import tkinter
inpath = 'InImage/1.png'
outpath = 'OutImage/'
iamge = cv.imread(inpath,1)#读取图片
'''
第一个参数:文件路径
第二个参数:   cv.IMREAD_COLOR： 加载彩色图像。任何图像的透明度都会被忽视。它是默认标志。
            cv.IMREAD_GRAYSCALE：以灰度模式加载图像
            cv.IMREAD_UNCHANGED：加载图像，包括alpha通道
            注意除了这三个标志，你可以分别简单地传递整数1、0或 - 1。
'''
cv.namedWindow('Image',cv.WINDOW_NORMAL)#创建窗口名
'''
在这种情况下，你可以指定窗口是否可调整大小。这是通过功能**cv.namedWindow**()完成的。
默认情况下，该标志为**cv.WINDOW_AUTOSIZE**。但是，
如果将标志指定为**cv.WINDOW_NORMAL**，
则可以调整窗口大小。当图像尺寸过大以及向窗口添加跟踪栏时，这将很有帮助。
'''
cv.imshow("Image",iamge)#显示图片
'''
第一个参数: 窗口名称
第二个参数: 读取的图片
'''
cv.waitKey(0)#等待键位按下（必须加）
cv.destroyWindow("Image")#销毁窗口
```
