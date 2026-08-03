---
title: "Linux安装ESP32idf"
date: 2025-08-30
categories: 
  - "esp32"
  - "linux"
---

## 安装需要软件

```
sudo apt-get install vim
sudo apt install git
```

如果没有Python3

```
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150
//可用以下命令切换python版本
sudo update-alternatives --config python
```

安装依赖

```
sudo apt-get install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0

sudo apt install python3.12-venv
```

安装最新版本idf

```
安装SDK

mkdir -p ~/esp
cd ~/esp
git clone -b v5.5 --recursive https://github.com/espressif/esp-idf.git
或者去
此存档也可以从 Espressif 的下载服务器下载：
```

[https://dl.espressif.com/github\_assets/espressif/esp-idf/releases/download/v5.5/esp-idf-v5.5.zip](https://dl.espressif.com/github_assets/espressif/esp-idf/releases/download/v5.5/esp-idf-v5.5.zip)

安装tool工具

```
添加执行权限：
chmod +x install.sh
下载：
cd ~/esp/esp-idf
./install.fish esp32c3
下载失败可以换源：
cd ~/esp/esp-idf
export IDF_GITHUB_ASSETS="dl.espressif.com/github_assets"
./install.sh
或者cd ~/esp/esp-idf
export IDF_GITHUB_ASSETS="dl.espressif.cn/github_assets"
./install.sh
```

设置全局变量

```
进入变量文件
cd ~/.bashrc
设置变量
alias get_idf='. $HOME/esp/v55/export.sh'
刷新文件
source ~/.bashrc
在任意目录输入
get_idf
即可build
也可进入.profile 文件配置
加入source ~/.bashrc
或者source esp/v55/export.sh
添加一个文件夹中所有文件权限添加x：
chmod -R u+x /path/to/program_directory/
```

编译

```
给串口权限
sudo usermod -aG dialout $USER
idf.py set-target esp32c3  # 明确目标芯片
idf.py build            # 仅编译
idf.py flash           #烧录
idf.py monitor         #打开串口

```
