---
title: "HTML"
date: 2024-09-22
categories: 
  - "html"
tags: 
  - "html"
coverImage: "12.jpg"
---

## 引用css和js

```
<link rel="stylesheet" href="youcss.css" />
<script type="text/javascript" src="example.js"></script>
```

## button跳转

```
<input type="button" onclick='location.href=("index.aspx")' />//在本页面打开
<input type="button" onclick='window.open("bedzhao.aspx")' />//打开新页面

<button onclick="window.location.href='../routeEdit/index.html'" type="button" id="add">新增</button>
```

## 载入图标库

```
 <link rel="stylesheet" href="https://lf3-cdn-tos.bytecdntp.com/cdn/expire-1-M/font-awesome/4.7.0/css/font-awesome.min.css">

用法：
<button><i class="fa fa-search" aria-hidden="true"></i></button>
```

  参考：[https://fa.fangguokeji.cn/](https://fa.fangguokeji.cn/)

## 按钮触发函数

```
<button type="button" onclick="closebox()">
```

## 表格+点击触发+移出

```
        <table class="table1">
		<tr>
			<td class="td" style="background:#330000" onClick="this.className='hov2'; SetColor('#330000')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#331900" onClick="this.className='hov2'; SetColor('#331900')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#333300" onClick="this.className='hov2'; SetColor('#333300')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#193300" onClick="this.className='hov2'; SetColor('#193300')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#003300" onClick="this.className='hov2'; SetColor('#003300')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#003319" onClick="this.className='hov2'; SetColor('#003319')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#003333" onClick="this.className='hov2'; SetColor('#003333')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#001933" onClick="this.className='hov2'; SetColor('#001933')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#000033" onClick="this.className='hov2'; SetColor('#000033')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#190033" onClick="this.className='hov2'; SetColor('#190033')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#330033" onClick="this.className='hov2'; SetColor('#330033')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#330019" onClick="this.className='hov2'; SetColor('#330019')" onMouseOut="this.className='td'">&nbsp;</td>
			<td class="td" style="background:#000000" onClick="this.className='hov2'; SetColor('#000000')" onMouseOut="this.className='td'">&nbsp;</td>
		</tr>
```

### 表格创建

```
<table class="min-w-full divide-y divide-gray-200">
                    <!-- 表头 -->
                    <thead class="bg-gray-50">
                        <tr>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider">
                                Customer ID
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider">
                                Email
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider">
                                Product
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider">
                                Status
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-semibold text-gray-500 uppercase tracking-wider">
                                Tracking
                            </th>
                        </tr>
                    </thead>
                    
                    <!-- 表格内容 -->
                    <tbody class="bg-white divide-y divide-gray-200">
                        <!-- 数据行1 -->
                        <tr class="table-hover-effect">
                            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                                CUST-78945
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                sarah.johnson@example.com
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                Wireless Headphones Pro
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <span class="status-badge bg-success/10 text-success">
                                    Delivered
                                </span>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-primary hover:text-primary/80 cursor-pointer transition-colors">
                                <i class="fa fa-truck mr-1"></i> TRK-987654
                            </td>
                        </tr>
                        
                        <!-- 数据行2 -->
                        <tr class="table-hover-effect">
                            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                                CUST-78946
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                michael.brown@example.com
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                Smart Watch Series 5
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <span class="status-badge bg-primary/10 text-primary">
                                    Shipped
                                </span>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-primary hover:text-primary/80 cursor-pointer transition-colors">
                                <i class="fa fa-truck mr-1"></i> TRK-987655
                            </td>
                        </tr>
                        
                        <!-- 数据行3 -->
                        <tr class="table-hover-effect">
                            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                                CUST-78947
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                emily.davis@example.com
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                Portable Bluetooth Speaker
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <span class="status-badge bg-warning/10 text-warning">
                                    Processing
                                </span>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <i class="fa fa-clock-o mr-1"></i> Pending
                            </td>
                        </tr>
                        
                        <!-- 数据行4 -->
                        <tr class="table-hover-effect">
                            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                                CUST-78948
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                david.wilson@example.com
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                Laptop Sleeve - Black
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <span class="status-badge bg-danger/10 text-danger">
                                    Cancelled
                                </span>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <i class="fa fa-times-circle mr-1"></i> N/A
                            </td>
                        </tr>
                        
                        <!-- 数据行5 -->
                        <tr class="table-hover-effect">
                            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                                CUST-78949
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                lisa.taylor@example.com
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-600">
                                USB-C Charging Cable Pack
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <span class="status-badge bg-pending/10 text-pending">
                                    On Hold
                                </span>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                <i class="fa fa-pause-circle mr-1"></i> Awaiting Stock
                            </td>
                        </tr>
                    </tbody>
                </table>
```
