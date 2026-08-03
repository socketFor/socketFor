---
title: "Git"
date: 2025-01-14
categories: 
  - "git"
---

### 一、git安装

[https://git-scm.com/downloads](https://git-scm.com/downloads)

### 二、配置git

```
 git config --global user.email johndoe@example.com
```

### 三、使用ssh连接

创建公钥

```
 ssh-keygen -t rsa -C "your_email@example.com"
```

一路回车

到用用户文件夹下.ssh文件中的id\_rsa.pub

登录你的github账号，从又上角的设置（ [Account Settings](https://github.com/settings) ）进入，然后点击菜单栏的 SSH key 进入页面添加 SSH key。

点击 Add SSH key 按钮添加一个 SSH key 。把你复制的 SSH key 代码粘贴到 key 所对应的输入框中，记得 SSH key 代码的前后不要留有空格或者回车。当然，上面的 Title 所对应的输入框你也可以输入一个该 SSH key 显示在 github 上的一个别名。默认的会使用你的邮件名称。

[https://www.cnblogs.com/yanglang/p/9563496.html](https://www.cnblogs.com/yanglang/p/9563496.html)

使用ssh命令尝试连接

```
ssh -T git@github.com
```

### 四、git命令

https://www.bilibili.com/video/BV1KarSY8E8v/?spm\_id\_from=333.337.search-card.all.click&vd\_source=c8acca57af0b5c7e066a91f435bcfdb7

[https://www.runoob.com/manual/git-guide](https://www.runoob.com/manual/git-guide)
