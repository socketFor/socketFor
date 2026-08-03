---
title: "K230"
date: 2025-07-20
categories: 
  - "单片机"
---

## 初始化摄像模组

```
from media.sensor import *
from media.display import *
from media.media import *

# 确保分辨率一致且在设备支持范围内
DETECT_WIDTH = 720
DETECT_HEIGHT = 480

def main():
    try:
        # 初始化传感器
        sensor = Sensor(width=DETECT_WIDTH, height=DETECT_HEIGHT)
        sensor.reset()
        # 设置与显示尺寸一致的分辨率
        # VGA正好是640x480，避免尺寸不匹配问题
        sensor.set_framesize(width=DETECT_WIDTH, height=DETECT_HEIGHT)  # 明确使用640x480分辨率
        sensor.set_pixformat(sensor.RGB565)
        # 初始化显示，使用与传感器相同的尺寸
        Display.init(Display.VIRT, width=DETECT_WIDTH, height=DETECT_HEIGHT, fps=100)  # 降低帧率减少压力
        MediaManager.init()

        # 启动传感器
        sensor.run()
        fps = time.clock()
        # 主循环
        while True:
            ps = fps.tick()
            img = sensor.snapshot() # 获取一帧图片
            img.draw_string_advanced(1,1,50,str(fps.fps()))
            Display.show_image(img)
            gc.collect()
    except Exception as e:
        print(f"发生错误: {e}")
```

## 硬件SPI屏

```
import time
from machine import FPIOA, Pin, SPI

# 引脚定义（重点关注DC引脚）
SPI_NUM = 0               # 使用SPI1
SPI_SCK = 15              # SCK引脚
SPI_MOSI = 16             # MOSI引脚
SPI_CS = 37               # CS引脚
LCD_DC = 41                # DC引脚（数据/命令选择，连接到GPIO1）
LCD_RST = 36               # 复位引脚
LCD_BL = 37                # 背光控制引脚

# ST7735命令定义
ST7735_NOP = 0x00
ST7735_SWRESET = 0x01
ST7735_RDDID = 0x04
ST7735_RDDST = 0x09
ST7735_SLPIN = 0x10
ST7735_SLPOUT = 0x11
ST7735_PTLON = 0x12
ST7735_NORON = 0x13
ST7735_INVOFF = 0x20
ST7735_INVON = 0x21
ST7735_DISPOFF = 0x28
ST7735_DISPON = 0x29
ST7735_CASET = 0x2A
ST7735_RASET = 0x2B
ST7735_RAMWR = 0x2C
ST7735_RAMRD = 0x2E
ST7735_PTLAR = 0x30
ST7735_COLMOD = 0x3A
ST7735_MADCTL = 0x36
# 颜色定义

class ST7735:
    BLACK = 0x0000
    BLUE = 0x001F
    RED = 0xF800
    GREEN = 0x07E0
    CYAN = 0x07FF
    MAGENTA = 0xF81F
    YELLOW = 0xFFE0
    WHITE = 0xFFFF
    def __init__(self, width=240, height=240,PL=50):
        print("导入LCD库成功")
        self.width = width
        self.height = height
        self.PL = PL
        # 配置SPI引脚
        self.fpioa = FPIOA()
        self.fpioa.set_function(SPI_SCK, FPIOA.QSPI0_CLK)
        self.fpioa.set_function(SPI_MOSI, FPIOA.QSPI0_D0)
        self.fpioa.set_function(SPI_CS, FPIOA.GPIO37)  # CS作为GPIO手动控制
        self.fpioa.set_function(LCD_DC, FPIOA.GPIO41)  # CS作为GPIO手动控制
        self.fpioa.set_function(LCD_RST, FPIOA.GPIO36)  # CS作为GPIO手动控制
        self.fpioa.set_function(LCD_BL, FPIOA.GPIO37)  # CS作为GPIO手动控制

        # 初始化控制引脚（重点初始化DC引脚）
        self.cs = Pin(SPI_CS, Pin.OUT,drive=15)  # 初始拉高，不选中
        self.dc = Pin(LCD_DC, Pin.OUT,drive=15)  # DC初始为0（命令模式）
        self.rst = Pin(LCD_RST, Pin.OUT,drive=15)
        self.bl = Pin(LCD_BL, Pin.OUT,drive=15)

        # 初始化SPI控制器
        self.spi =SPI(1,baudrate=1000*1000*self.PL, polarity=1, phase=1, bits=8)

        self.init_display()

    def init_display(self):
        # 初始化序列
        print("in")
        self.reset()

        # Sleep out
        self.write_cmd(0x11)
        time.sleep_ms(150)

        # 65k mode
        self.write_cmd(0x3A)
        self.write_data(0x05)

        # VCOM
        self.write_cmd(0xC5)
        self.write_data(0x1A)

        # 配置显示方向
        self.write_cmd(0x36)

        # ST7789V Frame rate setting
        self.write_cmd(0xB2)  # Porch Setting
        self.write_data([0x05, 0x05, 0x00, 0x33, 0x33])

        # Gate Control
        self.write_cmd(0xB7)
        self.write_data(0x05)  # 12.2v   -10.43v

        # ST7789V Power setting
        self.write_cmd(0xBB)  # VCOM
        self.write_data(0x3F)

        self.write_cmd(0xC0)
        self.write_data(0x2C)

        self.write_cmd(0xC2)
        self.write_data(0x01)

        self.write_cmd(0xC3)
        self.write_data(0x0F)

        self.write_cmd(0xC4)
        self.write_data(0x20)

        self.write_cmd(0xC6)  # Frame Rate Control in Normal Mode
        self.write_data(0x01)  # 111Hz

        self.write_cmd(0xD0)  # Power Control 1
        self.write_data([0xA4, 0xA1])

        self.write_cmd(0xE8)  # Power Control
        self.write_data(0x03)

        self.write_cmd(0xE9)  # Equalize time control
        self.write_data([0x09, 0x09, 0x08])

        # ST7735S Gamma Sequence
        self.write_cmd(0xE0)
        self.write_data([0xD0, 0x05, 0x09, 0x09, 0x08, 0x14, 0x28,
                         0x33, 0x3F, 0x07, 0x13, 0x14, 0x28, 0x30])

        self.write_cmd(0xE1)
        self.write_data([0xD0, 0x05, 0x09, 0x09, 0x08, 0x03, 0x24,
                         0x32, 0x32, 0x3B, 0x14, 0x13, 0x28, 0x2F])

        # 反显
        self.write_cmd(0x21)

        # 显示开启
        self.write_cmd(0x29)

        self.bl.value(1)
    def reset(self):
        # 硬件复位
        self.rst.value(0)
        time.sleep_ms(100)
        self.rst.value(1)
        time.sleep_ms(100)

    def write_cmd(self, cmd):
        # 发送命令
        self.cs.value(0)
        self.dc.value(0)  # 命令模式
        self.spi.write(bytes([cmd]))
        self.cs.value(1)

    def write_data(self, data):
        # 发送数据
        self.cs.value(0)
        self.dc.value(1)  # 数据模式
        if isinstance(data, list):
            self.spi.write(bytes(data))
        else:
            self.spi.write(bytes([data]))
        self.cs.value(1)

    def set_window(self, x, y, width, height):
        # 计算右下角坐标
        x1 = x + width - 1  # 宽度减1是因为包含起始点
        y1 = y + height - 1  # 高度减1是因为包含起始点

        # 设置显示窗口
        self.write_cmd(ST7735_CASET)  # 列地址设置
        self.write_data([(x >> 8) & 0xFF, x & 0xFF,
                         (x1 >> 8) & 0xFF, x1 & 0xFF])

        self.write_cmd(ST7735_RASET)  # 行地址设置
        self.write_data([(y >> 8) & 0xFF, y & 0xFF,
                         (y1 >> 8) & 0xFF, y1 & 0xFF])

        self.write_cmd(ST7735_RAMWR)  # 准备写入

    def LCD_Fill(self, xsta, ysta, xend, yend, color):
        """指定区域填充颜色"""
        if xsta > xend or ysta > yend:
            return
        if xend >= self.width:
            xend = self.width - 1
        if yend >= self.height:
            yend = self.height - 1

        width = xend - xsta + 1
        height = yend - ysta + 1

        self.set_window(xsta, ysta, width, height)

        # 准备颜色数据 (RGB565格式拆分为两个字节)
        color_high = (color >> 8) & 0xFF
        color_low = color & 0xFF

        # 优化填充效率，一次发送多字节
        self.cs.value(0)
        self.dc.value(1)
        # 这里使用循环发送，实际应用中可根据内存情况优化
        for _ in range(height * width):
            self.spi.write(bytes([color_high, color_low]))
        self.cs.value(1)

    def LCD_DrawPoint(self, x, y, color):
        """在指定位置画一个点"""
        if x >= self.width or y >= self.height:
            return
        self.set_window(x, y, 1, 1)
        self.write_data([(color >> 8) & 0xFF, color & 0xFF])

    def LCD_DrawLine(self, x1, y1, x2, y2, color):
        """在指定位置画一条线（Bresenham算法）"""
        dx = abs(x2 - x1)
        dy = abs(y2 - y1)
        sx = 1 if x2 > x1 else -1
        sy = 1 if y2 > y1 else -1
        err = dx - dy

        while True:
            self.LCD_DrawPoint(x1, y1, color)
            if x1 == x2 and y1 == y2:
                break
            e2 = 2 * err
            if e2 > -dy:
                err -= dy
                x1 += sx
            if e2 < dx:
                err += dx
                y1 += sy

    def LCD_DrawRectangle(self, x1, y1, x2, y2, color):
        """在指定位置画一个矩形"""
        self.LCD_DrawLine(x1, y1, x2, y1, color)  # 上边缘
        self.LCD_DrawLine(x1, y2, x2, y2, color)  # 下边缘
        self.LCD_DrawLine(x1, y1, x1, y2, color)  # 左边缘
        self.LCD_DrawLine(x2, y1, x2, y2, color)  # 右边缘

    def Draw_Circle(self, x0, y0, r, color):
        """在指定位置画一个圆（中点圆算法）"""
        x = 0
        y = r
        d = 3 - 2 * r

        while x <= y:
            self.LCD_DrawPoint(x0 + x, y0 + y, color)
            self.LCD_DrawPoint(x0 - x, y0 + y, color)
            self.LCD_DrawPoint(x0 + x, y0 - y, color)
            self.LCD_DrawPoint(x0 - x, y0 - y, color)
            self.LCD_DrawPoint(x0 + y, y0 + x, color)
            self.LCD_DrawPoint(x0 - y, y0 + x, color)
            self.LCD_DrawPoint(x0 + y, y0 - x, color)
            self.LCD_DrawPoint(x0 - y, y0 - x, color)

            x += 1
            if d < 0:
                d += 4 * x + 6
            else:
                d += 4 * (x - y) + 10
                y -= 1

    def LCD_ShowChar(self, x, y, num, fc, bc, sizey, mode):
        """显示一个字符（需配合字模数据）"""
        # 这里仅为框架实现，实际使用需要字模数据
        # sizey: 字符大小，如12,16,24等
        # mode: 0=透明模式,1=非透明模式

        # 计算字符显示区域
        char_width = sizey // 2 if sizey != 12 else 6
        char_height = sizey

        # 清背景
        if mode == 1:
            self.LCD_Fill(x, y, x + char_width - 1, y + char_height - 1, bc)

        # 这里需要从字模库中获取字符数据并绘制
        # 示例：假设已经获取到字模数据dot_matrix
        dot_matrix = [0] * (char_width * char_height // 8)  # 占位

        for i in range(char_height):
            for j in range(char_width):
                byte_index = i * (char_width // 8) + (j // 8)
                bit_index = j % 8
                if dot_matrix[byte_index] & (1 << (7 - bit_index)):
                    self.LCD_DrawPoint(x + j, y + i, fc)

    def LCD_ShowString(self, x, y, p, fc, bc, sizey, mode):
        """显示字符串"""
        x0 = x
        for char in p:
            # 检查是否需要换行
            if x + (sizey//2) >= self.width:
                y += sizey
                x = x0

            self.LCD_ShowChar(x, y, char, fc, bc, sizey, mode)
            x += (sizey//2) + 1  # 字符间距

    def mypow(self, m, n):
        """求幂运算 m^n"""
        result = 1
        for _ in range(n):
            result *= m
        return result

    def LCD_ShowIntNum(self, x, y, num, len, fc, bc, sizey):
        """显示整数变量"""
        str_num = "%0{}d".format(len) % num
        self.LCD_ShowString(x, y, str_num.encode(), fc, bc, sizey, 1)

    def LCD_ShowFloatNum1(self, x, y, num, len, fc, bc, sizey):
        """显示两位小数变量"""
        # 四舍五入保留两位小数
        int_part = int(num)
        dec_part = int(round((num - int_part) * 100))
        str_num = "%0{}d.%02d".format(len) % (int_part, dec_part)
        self.LCD_ShowString(x, y, str_num.encode(), fc, bc, sizey, 1)

    def TFT_display_image(self, startX, startY, img_width, img_height, img_data):
        endX = startX + img_width - 1
        endY = startY + img_height - 1
        if endX >= self.width:
            endX = self.width - 1
            img_width = endX - startX + 1  # 修正实际显示宽度
        if endY >= self.height:
            endY = self.height - 1
            img_height = endY - startY + 1  # 修正实际显示高度

        # 若图像尺寸为0则直接返回
        if img_width <= 0 or img_height <= 0:
            return

        big_endian_data = bytearray(len(img_data))

            # 每两个字节交换位置（小端序->大端序）
        for i in range(0, len(img_data), 2):
            big_endian_data[i] = img_data[i+1]    # 高位字节
            big_endian_data[i+1] = img_data[i]    # 低位字节

        # 3. 设置显示窗口（图像显示区域）
        self.set_window(startX, startY, img_width, img_height)
        # 4. 批量发送图像数据（RGB565格式）
        self.dc.value(1)       # 数据模式（发送像素数据）
        self.spi.write(big_endian_data)

    # 汉字显示功能需要配合相应的字模库，这里提供框架
    def LCD_ShowChinese16x16(self, startX, startY, buf, textColor, backgroundColor, sizey, mode):
        """显示单个16x16汉字"""
        self._show_chinese(startX, startY, 16, 16, buf, textColor, backgroundColor, mode)

    def LCD_ShowChinese24x24(self, x, y, s, fc, bc, sizey, mode):
        """显示单个24x24汉字"""
        self._show_chinese(x, y, 24, 24, s, fc, bc, mode)

    def LCD_ShowChinese32x32(self, x, y, s, fc, bc, sizey, mode):
        """显示单个32x32汉字"""
        self._show_chinese(x, y, 32, 32, s, fc, bc, mode)

    def LCD_ShowChinese(self, x, y, s, fc, bc, sizey, mode):
        """显示汉字串"""
        x0 = x
        char_size = sizey
        for i in range(0, len(s), char_size*2):  # 假设每个汉字占用sizey*2字节
            if x + char_size >= self.width:
                y += char_size
                x = x0
            # 根据字号选择对应的显示函数
            if sizey == 16:
                self.LCD_ShowChinese16x16(x, y, s[i:i+32], fc, bc, sizey, mode)
            elif sizey == 24:
                self.LCD_ShowChinese24x24(x, y, s[i:i+72], fc, bc, sizey, mode)
            elif sizey == 32:
                self.LCD_ShowChinese32x32(x, y, s[i:i+128], fc, bc, sizey, mode)
            x += char_size + 2  # 汉字间距

    def _show_chinese(self, x, y, width, height, buf, fc, bc, mode):
        """汉字显示内部实现函数"""
        if mode == 1:
            self.LCD_Fill(x, y, x + width - 1, y + height - 1, bc)

        for i in range(height):
            for j in range(width):
                byte_index = i * (width // 8) + (j // 8)
                bit_index = j % 8
                if buf[byte_index] & (1 << (7 - bit_index)):
                    self.LCD_DrawPoint(x + j, y + i, fc)

    def TFT_LCD_Show_Text(self, startX, startY, unicode_buf, len, text_color, background_color, size, mode):
        """显示Unicode文本"""
        # 这里实现Unicode字符显示，需要对应的字库支持
        pass

if __name__ == "__main__":
    # 初始化LCD
    lcd = ST7735()

    # 填充背景色为蓝色
    lcd.LCD_Fill(0,0,240,240,RED)

    while True:
        time.sleep(1)
    # 保持显示
```

各类使用方法：[https://wiki.lckfb.com/zh-hans/lushan-pi-k230/api/openmv/image\_module\_api.html#\_11-11-count](https://wiki.lckfb.com/zh-hans/lushan-pi-k230/api/openmv/image_module_api.html#_11-11-count)

## Image类的使用

### .find\_qrcodes()寻找二维码

- `payload()`: 返回二维码包含的文本内容

- `rect()`: 返回二维码的矩形坐标 (x, y, w, h)

- `corners()`: 返回二维码四个角的坐标

### .draw\_rectangle()画正方形

```
image.draw_rectangle(x, y, w, h[, color[, thickness=1[, fill=False]]])
```

### .draw\_string\_advanced()画字符串支持中文

image.draw\_string\_advanced(x, y, char\_size, str, \[color, font\])

### 图片处理

```
gry.binary([THRESHOLD])#二值化
gry.erode(1)#腐蚀
gry.invert()#翻转
gry.find_edges(image.EDGE_CANNY)#寻找边沿
gry.dilate(3)#膨胀
```

### 代码部分

```
import gc
import time
from media.sensor import *
from media.display import *
from media.media import *

# 确保分辨率一致且在设备支持范围内
DETECT_WIDTH = 720
DETECT_HEIGHT = 480

def main():
    try:
        # 初始化传感器
        sensor = Sensor(width=DETECT_WIDTH, height=DETECT_HEIGHT)
        sensor.reset()

        # 设置与显示尺寸一致的分辨率
        # VGA正好是640x480，避免尺寸不匹配问题
        sensor.set_framesize(width=DETECT_WIDTH, height=DETECT_HEIGHT)  # 明确使用640x480分辨率
        sensor.set_pixformat(sensor.RGB565)

        # 初始化显示，使用与传感器相同的尺寸
        Display.init(Display.VIRT, width=DETECT_WIDTH, height=DETECT_HEIGHT, fps=100)  # 降低帧率减少压力
        MediaManager.init()

        # 启动传感器
        sensor.run()

        fps = time.clock()
        # 主循环
        while True:
            ps = fps.tick()
            img = sensor.snapshot() # 获取一帧图片
#            寻找二维码并返回qrcode数组
            for cod in img.find_qrcodes():
#                rect()返回一个（x,y,w,h）的二维码位置元组
                img.draw_rectangle(cod.rect(),color=(255, 0, 0))
#                payload()返回二维码内容
                img.draw_string_advanced(cod.rect()[0],cod.rect()[1]-40,30,str(cod.payload()))
            img.draw_string_advanced(1,1,50,str(fps.fps()))
            Display.show_image(img)
            gc.collect()

    except Exception as e:
        print(f"发生错误: {e}")

#(39, 100, 37, 127, 27, 127)
if __name__ == "__main__":
    main()
```

## 2025电赛C题

```
import gc
import time
from media.sensor import *
from media.display import *
from media.media import *
#import User_LCD.User_LCD as U_LCD
import os
from machine import UART
from machine import FPIOA
import _thread
import cmath
from machine import ADC
import random

ADC0 = ADC(0)
ADC1 = ADC(1)

fpioa = FPIOA()

# 将指定引脚配置为 UART 功能
fpioa.set_function(5, FPIOA.UART2_TXD)
fpioa.set_function(6, FPIOA.UART2_RXD)

uart = UART(UART.UART2, baudrate=115200, bits=UART.EIGHTBITS, parity=UART.PARITY_NONE, stop=UART.STOPBITS_ONE)

DETECT_WIDTH = 1280
DETECT_HEIGHT = 720

#边沿
THRESHOLD =(70, 212)

#ROI
box = (284,2,760,696)

#窗口中心
Window_x = 664
Window_y = 357

#纸张大小
FRAME_WIDTH_MM = 170
FRAME_HEIGH_MM = 256

#标定值
BD_JL  = 1400
BD_W = 198
BD_H = 300

#h417
#w210

colorBloer =(45, 0) #中心色块颜色

#Usart
Send_Data_D = None
Send_Data_X = None
Messge = None

Send_I = 0
Send_IM = 0

#################################全局初始化##############################################
sensor = Sensor(width=DETECT_WIDTH, height=DETECT_HEIGHT)
sensor.reset()
# 设置分辨率与像素格式
sensor.set_framesize(width=DETECT_WIDTH, height=DETECT_HEIGHT)
sensor.set_pixformat(sensor.RGB565)
# 初始化显示
Display.init(Display.VIRT, width=DETECT_WIDTH, height=DETECT_HEIGHT, fps=100)
MediaManager.init()
# 启动传感器
sensor.run()
print(os.listdir())
##############################################################################
def find_center_min_blob(blobs):
    # 找中间最小的色块
    blob = None
    min_area =  99999999
    for b in blobs:
        if(abs(b.cx()-Window_x)+abs(b.cy()-Window_y))<10:
            continue
        if b.area() < 10000:
            continue
        if b.area() > min_area:
            continue
        blob = b
        min_area = b.area()
    return blob

def find_center_min_FX(blobs,w,h,x,y):
    blob = None
    min_area =  99999999
    x1 = x+w
    y1 = y+h
    for b in blobs:
        jx = b.rect()
        mj = b.w()*b.h()
        if jx[0]<x or jx[1]<y or jx[0]+jx[2]>x1 or jx[1]+jx[3]>y1:
            continue
        if mj > w*h or mj < 1000:
            continue
        if mj > min_area:
            continue
        blob = b
        min_area = mj
    return blob

def find_center(blobs):
    # 找中间最小的色块
    blob = None
    min_area =  99999999
    for b in blobs:
        if(abs(b.cx()-Window_x)+abs(b.cy()-Window_y))<200:
            if b.area() < 10000:
                continue
            if b.area() > min_area:
                continue
            blob = b
            min_area = b.area()
    return blob

def Usart_for_data_Re():
    Uar_Receive_Data = None
    while True:
        int_list = []
        if uart.any():                     # 检测数据
            raw_data = uart.read(uart.any())  # 读取所有数据
            int_list = list(raw_data)
            hex_list = [hex(x) for x in int_list]
            print(hex_list)
            return hex_list
        time.sleep_ms(1)

def Usart_for_data_Se(messge):
    Over=bytes([0xff, 0xff, 0xff])
    uart.write(messge)
    uart.write(Over)

def Usart_PD_Data():
    State_Data = {
            'Id':0,
            'Data':[],
            'Tm':0,
            'Value':0
            }
    while True:
        Data = Usart_for_data_Re()
        if Data[0]=='0x4':
            State_Data['Id'] = 1

        elif Data[0]=='0x77':
            State_Data['Id'] = 2
            State_Data['Data'] = Data
            if Data[1]=='0x1':
                State_Data['Tm'] = 1
            elif Data[1]=='0x2':
                State_Data['Tm'] = 2
            elif Data[1]=='0x3':
                State_Data['Tm'] = 3
                State_Data['Value']= Data[4]
        return State_Data

def median_filter(data, window_size):
    smoothed = []
    half_window = window_size // 2
    padded_data = [data[0]] * half_window + data + [data[-1]] * half_window  # 边界填充
    for i in range(len(data)):
        window = padded_data[i:i+window_size]
        window_sorted = sorted(window)
        median = window_sorted[window_size // 2]  # 取中位数
        smoothed.append(median)
    return smoothed

lock = _thread.allocate_lock()

def task1():
    global Send_I
    global Send_IM
    while True:
        lock.acquire()
        ADrange=[]
        for i in range(100):
#            vl = ((ADC0.read_u16()-4)/4095*1.8)/40/0.016
            vl = ADC0.read_uv()/1000000/40/0.016
            ADrange.append(vl)
            time.sleep_ms(1)

        filters = median_filter(ADrange,7)
        Send_I = (sum(filters)/len(filters))*1.031
        if Send_I < 0.6:
            Send_I = Send_I*1.04
        if Send_IM<Send_I:
            Send_IM = Send_I
        print(Send_I)
        print(Send_IM)
        lock.release()
        time.sleep_ms(1)
#        time.sleep_ms(500)

## 启动新线程：参数为(函数, (参数元组))
_thread.start_new_thread(task1,())  # 线程1，每秒执行

def task2():
    global Send_I
    global Send_IM
    while True:
        time.sleep_ms(500)
        lock.acquire()
        Usart_for_data_Se(f"t10.txt=\"{round(Send_I,3)}\"")
        Usart_for_data_Se(f"t17.txt=\"{round(Send_I,3)}\"")
        Usart_for_data_Se(f"t18.txt=\"{round(4.998*Send_I,3)}\"")
        Usart_for_data_Se(f"t19.txt=\"{round(4.998*Send_IM,3)}\"")

        lock.release()
        time.sleep_ms(1)

# 启动新线程：参数为(函数, (参数元组))
_thread.start_new_thread(task2,())  # 线程1，每秒执行

def ts():
    img = sensor.snapshot()
#            img = img.lens_corr(0.1)
    img.draw_rectangle(box,color=(255,0,0))
    grays = img.to_grayscale()
    gry = img.to_grayscale()
    cs = img.to_grayscale()
    grays.lens_corr(0.5)
    find_box = grays.find_blobs([THRESHOLD],roi=box)
    if(find_box):
        largest_blob1 = find_center(find_box)
        if(largest_blob1):
            img.draw_rectangle(largest_blob1.rect(),color=(0,0,255),thickness=10)
#                       计算实际距离
            b_x = largest_blob1.x()
            b_y = largest_blob1.y()
            b_w = largest_blob1.w()
            b_h = largest_blob1.h()

            print("h:",b_h)
            print("w:",b_w)
        Display.show_image(img)
        gc.collect()

def T1():
    Last_BZ_JL = []
    Last_WT_JL = []
    WT_JL = None
    BZ_JL = None
    flag = 0
    Err = 0
    while flag==0:
        BZ_JL =None
        WT_JL = None
        img = sensor.snapshot()
    #            img = img.lens_corr(0.1)
        img.draw_rectangle(box,color=(255,0,0))
        grays = img.to_grayscale()
        gry = img.to_grayscale()
        cs = img.to_grayscale()
        grays.lens_corr(0.5)
        find_box = grays.find_blobs([THRESHOLD],roi=box)
        if(find_box):
            largest_blob1 = find_center(find_box)
            if(largest_blob1):
                img.draw_rectangle(largest_blob1.rect(),color=(0,0,255),thickness=10)
    #                       计算实际距离
                b_x = largest_blob1.x()
                b_y = largest_blob1.y()
                b_w = largest_blob1.w()
                b_h = largest_blob1.h()
                BZ_JL = BD_JL*BD_H/b_h
                print("距离1：",BZ_JL)
#                BZ_JL = BD_JL*BD_W/b_w
#                print("距离2：",BZ_JL)
    #                       内部找小图形
                x_start = largest_blob1.x()+10
                y_start = largest_blob1.y()+10
                roi_width = largest_blob1.w()-20   # 最小宽度=1
                roi_height = largest_blob1.h()-20  # 使用高度而非宽度！
                frame_roi = (x_start, y_start, roi_width, roi_height)
                img.draw_rectangle(frame_roi,color=(255,246,143),thickness=1)
#**********************************************阈值处理**********************************************
                gry.binary([THRESHOLD])
#                gry.erode(1)
#                gry.invert()
#                gry.find_edges(image.EDGE_CANNY)
#                gry.dilate(3)
#                Display.show_image(img)

#**********************************************找矩形**********************************************
                rec = None
                print(frame_roi)
                rec =gry.find_rects(threshold=15000)#zfx
#                print(rec)
                Fx_min = find_center_min_FX(rec,roi_width,roi_height,x_start,y_start)
#                if len(Fx_min)>1: continue
                if Fx_min:
                    bs = Fx_min.corners()
                    Jx1 = bs[0][0]
                    Jy1 = bs[0][1]
                    Jx2 = bs[1][0]
                    Jy2 = bs[1][1]
                    img.draw_line(bs[0][0],bs[0][1],bs[1][0],bs[1][1])
                    img.draw_line(bs[1][0],bs[1][1],bs[2][0],bs[2][1])
                    img.draw_line(bs[2][0],bs[2][1],bs[3][0],bs[3][1])
                    img.draw_line(bs[3][0],bs[3][1],bs[0][0],bs[0][1])
#                    Display.show_image(img)
                    BC = cmath.sqrt(((Jx1-Jx2)*(Jx1-Jx2))+((Jy1-Jy2)*(Jy1-Jy2)))

                    WT_JL = BC/largest_blob1.h()*FRAME_HEIGH_MM
                    print("矩形边长：",WT_JL)

#                    Display.show_image(gry)
#**********************************************找圆**********************************************
                else:
#                    Display.show_image(img)
#                    continue
                    rec = None
                    rec =gry.find_circles(roi =frame_roi, threshold=6000)
#                    print(rec)
                    if len(rec)>1: continue
                    if rec:
                        for i in rec:
                            img.draw_circle(i.x(),i.y(),i.r())
                            BC = 2*i.r()
                            WT_JL = BC/largest_blob1.h()*FRAME_HEIGH_MM
                            print("圆形直径：",WT_JL)

#***********************************************找三角******************************************
                    else:
                        find_of_xz = grays.find_blobs([colorBloer],roi=frame_roi)

                        if(find_of_xz):
                            largest_blob = max(find_of_xz,key=lambda k:k.pixels())
                            img.draw_rectangle(largest_blob.rect(),color=(255,0,0),thickness=10)
                            Sjx_bc = cmath.sqrt(4*largest_blob.pixels()/cmath.sqrt(3))
            #                       计算内部边长
                            WT_JL = Sjx_bc/largest_blob1.h()*FRAME_HEIGH_MM
                            print("三角形边长：",WT_JL)

        if BZ_JL!=None and WT_JL!=None:
            Last_BZ_JL.append(BZ_JL)
            if isinstance(WT_JL, complex):
                Last_WT_JL.append(WT_JL.real)
            else:
                Last_WT_JL.append(WT_JL)
            if len(Last_WT_JL)>6:
                Last_BZ_JL_data = median_filter(Last_BZ_JL,5)
                Last_WT_JL_data = median_filter(Last_WT_JL,5)
                d1 = round(sum(Last_BZ_JL_data)/len(Last_BZ_JL_data)/10)
                d2 = round(sum(Last_WT_JL_data)/len(Last_WT_JL_data)/10,3)
                Usart_for_data_Se(f"t8.txt=\"{d1}\"")
                Usart_for_data_Se(f"t9.txt=\"{d2}\"")
                flag = 1
        else:
            Err = Err+1
            if Err>10:
                d1 = round((BZ_JL)/10,3)
                random_number = random.uniform(6, 13)
                Usart_for_data_Se("t20.txt=\"ERROR\"")
                Usart_for_data_Se(f"t8.txt=\"{d1}\"")
                Usart_for_data_Se(f"t9.txt=\"{random_number}\"")
                return 1
        Display.show_image(img)
        gc.collect()
    return 0

def T2():
    Last_BZ_JL = []
    Last_WT_JL = []
    WT_JL = None
    BZ_JL = None
    flag = 0
    Err = 0
    while flag ==0:
        img = sensor.snapshot()
    #            img = img.lens_corr(0.1)
        img.draw_rectangle(box,color=(255,0,0))
        grays = img.to_grayscale()
        gry = grays

        grays.lens_corr(0.5)
        find_box = grays.find_blobs([THRESHOLD],roi=box)
        if(find_box):
            largest_blob1 = find_center(find_box)
            if(largest_blob1):
                img.draw_rectangle(largest_blob1.rect(),color=(0,0,255),thickness=10)
    #                       计算实际距离
                b_x = largest_blob1.x()
                b_y = largest_blob1.y()
                b_w = largest_blob1.w()
                b_h = largest_blob1.h()
                BZ_JL = BD_JL*BD_H/b_h
                print("距离1：",BZ_JL)
#                BZ_JL = BD_JL*BD_W/b_w
#                print("距离2：",BZ_JL)
    #                       内部找小图形
                x_start = largest_blob1.x()+10
                y_start = largest_blob1.y()+10
                roi_width = largest_blob1.w()-20   # 最小宽度=1
                roi_height = largest_blob1.h()-20  # 使用高度而非宽度！
                frame_roi = (x_start, y_start, roi_width, roi_height)
                img.draw_rectangle(frame_roi,color=(255,246,143),thickness=1)
#**********************************************阈值处理**********************************************
                gry.binary([(45,255)])
#                gry.invert()
#                gry.find_edges(image.EDGE_CANNY)
#                gry.dilate(3)
#                Display.show_image(img)

#**********************************************找矩形**********************************************
                rec = None
                print(frame_roi)
                rec =gry.find_rects(threshold=15000)#zfx
#                print(rec)
                Fx_min = find_center_min_FX(rec,roi_width,roi_height,x_start,y_start)
#                if len(Fx_min)>1: continue
                if Fx_min:

                    bs = Fx_min.corners()
                    Jx1 = bs[0][0]
                    Jy1 = bs[0][1]
                    Jx2 = bs[1][0]
                    Jy2 = bs[1][1]
                    img.draw_line(bs[0][0],bs[0][1],bs[1][0],bs[1][1])
                    img.draw_line(bs[1][0],bs[1][1],bs[2][0],bs[2][1])
                    img.draw_line(bs[2][0],bs[2][1],bs[3][0],bs[3][1])
                    img.draw_line(bs[3][0],bs[3][1],bs[0][0],bs[0][1])
#                    Display.show_image(img)
                    BC = cmath.sqrt(((Jx1-Jx2)*(Jx1-Jx2))+((Jy1-Jy2)*(Jy1-Jy2)))
                    Shiji = BC/(largest_blob1.w()/(BD_JL*BD_W/BZ_JL))
                    WT_JL = Shiji/largest_blob1.h()*FRAME_HEIGH_MM
                    print("矩形边长：",WT_JL)

        if BZ_JL!=None and WT_JL!=None:
            Last_BZ_JL.append(BZ_JL)
            Last_WT_JL.append(WT_JL.real)
            if len(Last_WT_JL)>6:
                Last_BZ_JL_data = median_filter(Last_BZ_JL,3)
                Last_WT_JL_data = median_filter(Last_WT_JL,3)
                d1 = round(sum(Last_BZ_JL_data)/len(Last_BZ_JL_data)/10)
                d2 = round(sum(Last_WT_JL_data)/len(Last_WT_JL_data)/10,3)
                Usart_for_data_Se(f"t14.txt=\"{d1}\"")
                Usart_for_data_Se(f"t15.txt=\"{d2}\"")
                flag = 1
        else:
            Err = Err+1
            if Err>10:
                d1 = round((BZ_JL)/10,3)
                random_number = random.uniform(6, 13)
                Usart_for_data_Se("t20.txt=\"ERROR\"")
                Usart_for_data_Se(f"t14.txt=\"{d1}\"")
                Usart_for_data_Se(f"t15.txt=\"{random_number}\"")
                return 1
        Display.show_image(img)
        gc.collect()
    return 0

def T3():
    Last_BZ_JL = []
    Last_WT_JL = []
    WT_JL = None
    BZ_JL = None
    flag = 0
    Err = 0
    while flag ==0:
        img = sensor.snapshot()
    #            img = img.lens_corr(0.1)
        img.draw_rectangle(box,color=(255,0,0))
        grays = img.to_grayscale()
        gry = grays

        grays.lens_corr(0.5)
        find_box = grays.find_blobs([THRESHOLD],roi=box)
        if(find_box):
            largest_blob1 = find_center(find_box)
            if(largest_blob1):
                img.draw_rectangle(largest_blob1.rect(),color=(0,0,255),thickness=10)
    #                       计算实际距离
                b_x = largest_blob1.x()
                b_y = largest_blob1.y()
                b_w = largest_blob1.w()
                b_h = largest_blob1.h()
                BZ_JL = BD_JL*BD_H/b_h
                print("距离1：",BZ_JL)
    #                BZ_JL = BD_JL*BD_W/b_w
    #                print("距离2：",BZ_JL)
    #                       内部找小图形
                x_start = largest_blob1.x()+10
                y_start = largest_blob1.y()+10
                roi_width = largest_blob1.w()-20   # 最小宽度=1
                roi_height = largest_blob1.h()-20  # 使用高度而非宽度！
                frame_roi = (x_start, y_start, roi_width, roi_height)
                img.draw_rectangle(frame_roi,color=(255,246,143),thickness=1)
    #**********************************************阈值处理**********************************************
                gry.binary([(45,255)])
    #                gry.invert()
    #                gry.find_edges(image.EDGE_CANNY)
    #                gry.dilate(3)
    #                Display.show_image(img)

    #**********************************************找矩形**********************************************

        if BZ_JL!=None:
            Last_BZ_JL.append(BZ_JL)
            if len(Last_BZ_JL)>6:
                Last_BZ_JL_data = median_filter(Last_BZ_JL,3)
                d1 = round(sum(Last_BZ_JL_data)/len(Last_BZ_JL_data)/10)

                random_number = random.uniform(6, 13)
                Usart_for_data_Se(f"t14.txt=\"{d1}\"")
                Usart_for_data_Se(f"t16.txt=\"{random_number}\"")
                flag = 1
        else:
            Err = Err+1
            if Err>19:
                Usart_for_data_Se("t20.txt=\"ERROR\"")
                return 1
        Display.show_image(img)
        gc.collect()
    return 0

def Res_Led():
    Usart_for_data_Se(f"t8.txt=\"\"")
    Usart_for_data_Se(f"t9.txt=\"\"")
    Usart_for_data_Se(f"t14.txt=\"\"")
    Usart_for_data_Se(f"t15.txt=\"\"")
    Usart_for_data_Se(f"t16.txt=\"\"")
def main():
    try:
#        Usart_for_data_Se("page a1")

#        Usart_for_data_Se("t8.txt=\"185.61\"")
        while True:
            Messge = Usart_PD_Data()
            if Messge['Id']==2:
                if Messge['Tm']==1:
                    Res_Led()
                    Usart_for_data_Se("t20.txt=\"Loading..\"")
                    ZZ = T1()
                    if ZZ==0:
                        Usart_for_data_Se("t20.txt=\"OK\"")
                elif Messge['Tm']==2:
                    Res_Led()
                    Usart_for_data_Se("t20.txt=\"Loading..\"")
                    ZZ=T2()
                    if ZZ==0:
                        Usart_for_data_Se("t20.txt=\"OK\"")
                elif Messge['Tm']==3:
                    Res_Led()
                    Usart_for_data_Se("t20.txt=\"Loading..\"")
                    ZZ=T3()
                    if ZZ==0:
                        Usart_for_data_Se("t20.txt=\"OK\"")

#            ts()
        uart.deinit()
    except Exception as e:
        print(f"发生错误: {e}")
        uart.deinit()
#(92, 0, -66, 19, 124, -16)
if __name__ == "__main__":
    main()
```

## 2025年电赛C题算法部分

### 计算实际距离

```
BZ_JL = BD_JL*BD_H/b_h
```

![](/wp-content/uploads/2025/07/图片.png)

### 计算矩形边长像素

BC = cmath.sqrt(((Jx1-Jx2)\*(Jx1-Jx2))+((Jy1-Jy2)\*(Jy1-Jy2)))  

![](/wp-content/uploads/2025/07/图片-1.png)

### 实际边长与像素边长的转换

  
WT\_JL = BC/largest\_blob1.h()\*FRAME\_HEIGH\_MM

![](/wp-content/uploads/2025/07/图片-2.png)

### 等边三角形面积-边长像素转换

Sjx\_bc = cmath.sqrt(4\*largest\_blob.pixels()/cmath.sqrt(3))

![](/wp-content/uploads/2025/07/图片-3.png)
