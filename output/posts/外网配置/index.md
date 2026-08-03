---
title: "外网配置"
date: 2024-07-12
categories: 
  - "server"
---

## 一、首先看你的网络是否具有公网IPV4

1. 打开[https://www.lddgo.net/network/getmyip](https://www.lddgo.net/network/getmyip)
    - ![](/wp-content/uploads/2024/07/1.png)
    
    - 在这个网页会显示出你的公网IPV4\\IPV6
    
    - 显示出公网ip后若你的公网ip显示196.XXX.XXX开始的那就没有公网ip！！（可以选择内网穿透）

3. 看到公网ip后打开你家的光猫后台（光猫背后的管理地址例如：192.168.1.1）
    - 拿我家光猫举例：![](/wp-content/uploads/2024/07/2.png)
    
    - 打开后如果你电脑显示的公网ipv4和光猫的对上那就说明你家网有公网IPV4

有公网ip后就具有配置一台服务器的基本需求了，但是有公网ip后还需要一个域名因为家庭的公网ip基本上都是动态的需要通过ddns同步ip到域名用域名访问服务器。

## 二、购买域名

[https://blog.csdn.net/pang\_ping/article/details/101279960](https://blog.csdn.net/pang_ping/article/details/101279960)

## 三、腾讯云dns绑定

阿里云注册域名配置为 DNSPod 的 DNS 服务器：[https://docs.dnspod.cn/dns/alibaba-changedns](https://docs.dnspod.cn/dns/alibaba-changedns)

dns配置：[https://cloud.tencent.com/developer/article/1883047](https://cloud.tencent.com/developer/article/1883047)

ddns配置：[https://www.bilibili.com/video/BV1Av411p72v/?spm\_id\_from=333.337.search-card.all.click&vd\_source=c8acca57af0b5c7e066a91f435bcfdb7](https://www.bilibili.com/video/BV1Av411p72v/?spm_id_from=333.337.search-card.all.click&vd_source=c8acca57af0b5c7e066a91f435bcfdb7)

## 四、电脑开启远程桌面

![](/wp-content/uploads/2024/07/3.png)

## 五、端口映射

因为要外网远程桌面连接需要将远程桌面的端口映射到外网，而远程桌面的端口为3389

所以需要把3389的端口映射出去。

映射端口我们只需要进入光猫的管理地址，在高级设置里面找到端口映射

![](/wp-content/uploads/2024/07/4-1024x468.png)

如果碰到连接失败要考虑

1. 电脑防火墙是否全部关闭

3. 路由器防火墙是否关闭

5. 端口是否被运营商禁止

7. 端口是否输入正确

## 六、端口被运营商禁止

一般家庭的端口的80 和 443端口会被禁止。碰到这种情况我们只需要改端口就好了就比如说fdf.email这个网址在浏览器直接访问浏览器默认将这个网站的端口看成80端口，也就是浏览器自动将这个地址变成fdf.email:80。所以我们如果不用80端口访问网站时就要自己输入端口例如我的网站端口是1134所以我的网址要访问只能fdf.email

但是有些运营商会把远程3389端口禁止所以我们要更改只能修改3389的端口如何修改可以看

[https://blog.csdn.net/weixin\_42951763/article/details/106461750](https://blog.csdn.net/weixin_42951763/article/details/106461750)。
