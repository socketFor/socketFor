---
title: "Linux 配置磁盘内存"
date: 2025-09-07
categories: 
  - "linux"
---

## 安装gparted

```
sudo apt install gparted
```

## 解除只读权限

```
sudo -i
mount -o remount -rw /
mount -o remount -rw /var/snap/firefox/common/host-hunspell
```

## 扩容

右键resize

![](images/图片.png)
