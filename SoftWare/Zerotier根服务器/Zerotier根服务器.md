---
title: Zerotier根服务器
date: 2026-04-07
tags: [软件工具]
type: guide
status: complete
---

# Zerotier根服务器

[https://post.smzdm.com/p/apxkx2m7/](https://post.smzdm.com/p/apxkx2m7/)

# 外网访问家庭局域网方案

随着NAS、软路由的普及，越来越多的人产生了外网访问家庭网络的需求。当前访问家庭网络的方案主要分为三类：

## **公网ip**

这一类当然是最方便的，也是花费较少的方案。可以通过ip地址直接访问家庭网络，即使是变动的公网ip，也只需购买域名配置DDNS即可。但随着政策的加严，获取ipv4公网ip的渠道越来越少，之前寄托了很多希望的ipv6现在看来也存在诸多问题。

# **隧道穿透**

这一类是通过一台具有公网ip的机器（通常是一台云[服务器](https://www.smzdm.com/fenlei/fuwuqi/)）作为中转，在这台机器与家庭局域网内的机器建立隧道，借由公网下的服务器作为跳板访问家庭网络。这一类服务有自建的，如Frp、NPS等，也有商家提供服务的，如SakuraFrp、DDNSTO等。

# **虚拟局域网**

这一类更适合私人或小团体使用，其原理是建立一个虚拟的广域网，并为加入这个虚拟网络的设备分配一个虚拟的ip，并在这个广域网内通过虚拟ip互访。最知名的代表就是Zerotier，也是我今天所要介绍的

## Zerotier的优缺点

通俗来讲就是 Peer to Peer (P2P) ，ZeroTier 根服务器记录请求路径来做类似于DNS的解析。主要功能，就是可以把多个不同网络的设备连接在一起，用来就像在一个局域网下 。

Zerotier的优点在于其部署十分简便，只需在zerotier官网注册登陆并创建网络，在自己的设备安装客户端加入网络后，zerotier就会自动分配虚拟ip，无需了解ip、端口等配置。

但很可惜，zerotier的根服务器都在国外，我们到zerotier服务器需要经过层层的NAT中转，这导致我们通过zerotier网络互访时不仅存在较大的延迟，而且在网络高峰段非常容易断连。

在之前，对于这种情况，我们的解决方案是在国内的服务器上搭建一个zerotier的moon中转节点，作为zerotier根服务器的代理加速，但加速效果有好有坏（有的人说速度很快，但我的中转就很慢），而且这种方式的一个问题就是，**安卓端的[手机](https://www.smzdm.com/fenlei/zhinengshouji/)无法加入moon节点**。这个问题很快就被解决了，kaaass大佬做了一个[ZerotierFix](https://github.com/kaaass/ZerotierFix)的app，相比于官方增加了可以加入moon节点的功能。

## **Zerotier-planet**

虽然大佬的app解决了安卓客户端不能加入moon节点的问题，但moon节点的搭建以及客户端的配置仍然不是一个比较简单的过程，又要配置服务器端又要配置客户端，zerotier便捷性的优点就体现不出来了。

前段时间，小众软件推荐的一分钟自建 zerotier-planet 行星服务器吸引了我，简单浏览了一下，发现过程是真的很简单。jonnyan404大佬的这个项目是基于另一个项目ztncui优化而来，简单而言就是用docker部署一个独立的zerotier虚拟网络。部署这个项目的全部准备只有在你的公网服务器上安装好docker和docker-compose。

jonnyan404大佬已经写好了zerotier-planet的docker配置脚本，我们只需登陆服务器执行以下几个命令

1. 从gitee仓库下载docker配置文件

git clone [https://gitee.com/Jonnyan404/zerotier-planet](https://gitee.com/Jonnyan404/zerotier-planet)

2. 进入下载的项目文件夹

cd zerotier-planet

3. docker安装zerotier-planet，如果服务器是Ubuntu系统，记得命令前加上 sudo

docker-compose up -d

4. 然后就可以访问 http://ip:4000 访问web界面了（记得防火墙开放4000端口）

默认用户名:admin 默认密码:mrdoc.fun

建议登陆后立即创建新用户，并切换到新用户登陆，删除默认的admin账户，以保证安全

接下来的操作与官方版的zerotier没有太大区别了，这里新建一个虚拟网络**test**，ID是

**119c41e47a8a0433**。

zerotier-planet

![](Zerotier根服务)A8/61b0b4be51a064572.png_e1080.jpg

**Private:** 设置对设备的访问控制权，默认勾选上

![](Zerotier根服务)A8/61b0b6beb1e503113.png_e1080.jpg

Private

**Easy setup:** 设置虚拟广域网段，可以自己设置

![](Zerotier根服务)A8/61b0b70e3248e2374.png_e1080.jpg

Easy setup

**Routes:** 设置路由，一般就只有一个默认的路由（也就是刚刚设置的网段），稍后介绍它的扩展用法

![](Zerotier根服务)A8/61b0b74b4338f6875.png_e1080.jpg

Routes

**Assignment Pools:** ip地址分配，你可以指定该网段下虚拟ip可供分配的范围

Assignment Pools

![](Zerotier根服务)A8/61b0b7c8544515456.png_e1080.jpg

接下来，只要在自己的设备上安装zerotier的客户端，然后通过ID加入网络，就可以在该页面刷新出设备列表了，为新设备勾选Authorized即可分配虚拟的ip地址了。

![](Zerotier根服务)A8/61b0ba894335446.png_e1080.jpg

ip分配

基于zerotier网络可以实现许多功能，例如windows端的远程桌面连接，ssh连接linux服务器，访问个人服务器上的博客

## 应用扩展-允许openwrt客户端自动NAT

尽管zerotier已经支持相当多的平台了，但仍有一些设备不受支持，比如我升级到DSM 7.0的小猫盘。为了让这些无法安装zerotier客户端的设备也加入到这个虚拟网络中，我们需要一个支持zerotier的openwrt[路由器](https://www.smzdm.com/fenlei/luyouqi/)。以我的R2S为例

**zerotier设置**

1. 首先将R2S也加入虚拟网络，在openwrt后台管理中填入虚拟网络ID，并允许客户端NAT。

![](Zerotier根服务)A8/61b0c0badbc062914.png_e1080.jpg

R2S-Openwrt

2. 设置虚拟网络路由

![](Zerotier根服务)A8/61b0d2039a55d5702.png_e1080.jpg

R2S-虚拟ip

我的R2S分到的虚拟ip为**10.81.33.114**，在**Routes**我们再新建一个路由

![](Zerotier根服务)A8/61b0d346c21d46333.png_e1080.jpg

新建路由

我这里已经设置过了，**Target**应根据路由器的网段来决定，比如我的R2S局域网访问地址为**192.168.2.1**，则**Target**则应填**192.168.2.0/24**，**Gateway**则填R2S分配到的虚拟ip，这里是**10.81.33.114**。

### openwrt设置

除了zerotier上的设置，还要对openwrt进行设置，使得流量可以通过虚拟广域网传播

新建zerotier接口

![](Zerotier根服务)A8/61b0d872d88e01443.png_e1080.jpg

我们在openwrt **网络>接口** 里新增一个zerotier接口，系统会为其分配一个zt开头的物理接口，我的是**zt6xtbbu66**。对该接口进行设置，**传输协议**选择**静态地址**，**ipv4地址**填分配的虚拟ip，**ipv4子网掩码**是默认的**225.225.225.0**，在**防火墙设置**中**创建/分配防火墙区域**选择**lan**

接着在 **网络>防火墙** 里自定义防火墙规则，增加以下规则

iptables -I FORWARD -i **zt6xtbbu66** -j ACCEPT

iptables -I FORWARD -o **zt6xtbbu66** -j ACCEPT

iptables -t nat -I POSTROUTING -o **zt6xtbbu66** -j MASQUERADE

记得将**zt6xtbbu66**换成你自己对应的物理接口，重启防火墙

**测试**

这个时候，R2S下的设备应当已经加入zerotier广域网了，以我的小猫盘为例，在局域网下它的访问地址为192.168.2.147:5000，在[安卓手机](https://www.smzdm.com/fenlei/androidshouji/)上通过ZerotierOne连接zerotier广域网，并断开WiFi，在浏览器中访问192.168.2.147:5000

![](Zerotier根服务)A8/61b0dd41671b75438.jpg_e1080.jpg

小猫盘

访问成功，表明小猫盘已经通过R2S接入到局域网了

作者声明本文无利益相关，欢迎值友理性交流，和谐讨论～