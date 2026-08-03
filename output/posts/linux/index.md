---
title: "Linux"
date: 2024-10-31
categories: 
  - "linux"
tags: 
  - "linux"
---

## 常用命令

### 列出文件

- ls -a 显示隐藏文件

- ls -l 显示文件全部属性

### 解压文件

- tar -xvf archive.tar 解压archive.tar 文件

- tar -zcvf archive.tar.gz directory 打包directory目录下的文件文件名为archive.tar.gz

### 安装openserver-ssh

- sudo apt install openssh-server 安装

- sudo service ssh start 启动

- sudo service ssh status 查看状态

- sudo systemctl enable ssh 开机自启

### 更改权限

- su - //更改root权限

- su passwd root //更改root密码

### 关闭防火墙

- systemctl stop ufw //临时关闭防火墙

- systemctl status ufw //查看防火墙状态

- systemctl disable ufw //关闭防火墙开机启动

### APT安装管理命令

- apt install <pack-name>//安装包

- apt remove <pack-name>//卸载包

### 常用快捷键

- ctrl + c //停止运行

- ctrl + l // 清屏

- ctrl + q // 退出

- tab //提示

- ctrl + u // 清除当前命令

### 文件操作

- mkdir //创建文件夹
    - mkdir -p //确保目录存在，不存在就创建

- touch //创建文件

- rm //完全删除文件
    - rm <file> //删除文件
    
    - rm -r //删除整个文件夹

- mv //移动
    - mv -r <file> /tmp //移动到回收站

- cat //查看文件

- ln //软链接

- history //查看历史命令

- cp 复制
    - cp -r 复制根目录文件夹 复制到的位置

## Vim操作

- dd //删除整行

- yy //复制整行

- p //粘贴到当前

- u //撤销

- insert //进入编辑模式

- esc //退出编辑模式

- a //进入编辑模式光标到字符后

- i //进入到编辑模式光标到字符前

- o //进入到编辑模式光标到下一行

- :wq //保存退出

- :!强制执行

- / //搜索模式

### 权限操作

- chmod //权限操作

- deluser //删除用户包括home目录

- userdel //删除用户不包括Home目录

- useradd //添加用户不包括home目录

- adduser //添加用户包括home目录

- chown //修改所属目录

- chgrp //修改所属组

- groupadd //添加组

### Linux命令大全

[![](/wp-content/uploads/2024/10/Snipaste_2024-10-31_20-25-40-1024x527.png)](https://www.runoob.com/linux/linux-command-manual.html)

[https://www.runoob.com/linux/linux-command-manual.html](https://www.runoob.com/linux/linux-command-manual.html)

### 文件属性

![](/wp-content/uploads/2024/10/1.png)

![](/wp-content/uploads/2024/10/Snipaste_2024-11-06_13-29-05-1.png)

### 权限练习

![](/wp-content/uploads/2024/10/Snipaste_2024-11-06_13-29-05.png)

- 创建zhangzong用户
    - sudo adduser zhangzong

- 添加root权限
    - sudo vim /etc/sudoers
    
    - zhangzong all=(all:all) nopasswd:all
    
    - :wq!强制保存退出

- 1
