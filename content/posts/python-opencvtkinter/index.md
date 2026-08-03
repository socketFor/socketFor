---
title: "Python-Opencv+tkinter"
date: 2024-12-08
categories: 
  - "python"
coverImage: "back.jpg"
---

### 色域调整

```
import tkinter as tkimport cv2 as cvfrom tkinter import messageboximport  numpyinpath = 'InImage/1.png'outpath = 'OutImage/R.png'i1 = []def cutphoto(*args):    global i1    try:        iamge = cv.imread(inpath, 0)  # 读取图片        image = cv.resize(iamge,args) #设置图片大小        ret,i1 = cv.threshold(image,int(scale.get()),255,cv.THRESH_TRUNC) #调整色域        cv.namedWindow('Image', cv.WINDOW_AUTOSIZE)  # 创建窗口名        cv.imshow("Image",i1)  # 显示图片    except Exception as e:        print(e)def RGB(*args):    if ipt1.get() and ipt2.get():        w = int(ipt1.get())        h = int(ipt2.get())        if w > 2000 or h > 2000:            messagebox.showinfo(title='提示',message='设置图片过大')        else:            cutphoto(w,h)    else:        w = 500        h = 500        cutphoto(w, h)def savepr():    RGB()    cv.imwrite(outpath, i1)    tk.messagebox.showinfo(title='提示',message='保存成功')parent = tk.Tk()parent.title("RGB色域分解")# 设置窗口大小、居中width = 461height = 387screenwidth = parent.winfo_screenwidth()screenheight = parent.winfo_screenheight()geometry = '%dx%d+%d+%d' % (width, height, (screenwidth - width) / 2, (screenheight - height) / 2)parent.geometry(geometry)parent.resizable(width=False, height=False)label1 = tk.Label(parent, text="图片大小设置", anchor="center", )label1.place(x=161, y=28, width=129, height=30)label2 = tk.Label(parent, text="长度：", anchor="center", )label2.place(x=96, y=101, width=50, height=30)label3 = tk.Label(parent, text="宽度：", anchor="center", )label3.place(x=95, y=162, width=50, height=30)label4 = tk.Label(parent, text="px", anchor="center", )label4.place(x=313, y=98, width=50, height=30)label5 = tk.Label(parent, text="px", anchor="center", )label5.place(x=313, y=165, width=50, height=30)ipt1 = tk.Entry(parent, )ipt1.place(x=165, y=97, width=150, height=30)ipt2 = tk.Entry(parent, )ipt2.place(x=165, y=163, width=150, height=30)scale = tk.Scale(parent, to=255, orient=tk.HORIZONTAL, command=RGB)scale.set(127)scale.place(x=110, y=226, width=241, height=30)btn = tk.Button(parent, text="确认", takefocus=False, command=RGB)btn.place(x=102, y=315, width=100, height=30)btn1 = tk.Button(parent, text="保存", takefocus=False, command=savepr)btn1.place(x=287, y=315, width=100, height=30)tk.mainloop()
```
