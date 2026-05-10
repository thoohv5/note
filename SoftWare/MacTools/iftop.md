---
title: iftop
date: 2026-04-07
tags:
  - 软件工具
type: guide
status: complete
---

## iftop

## 概述

Linux系统下即时监控服务器的网络带宽使用情况

iftop 是 Linux 系统一个免费的网卡实时流量监控工具，类似于 top 命令。iftop 可以监控指定网卡的实时流量、端口连接信息、反向解析 IP 等，还可以精确显示本机网络流量及网络内各主机和本机相互通信的流量集合，非常适合于监控代理服务器或路由器的网络流量。

备注：以root身份才能运行

## 安装

1. 通过软件管理工具安装

```
## CentOS
$ sudo yum install iftop

## Ubuntu
$ sudo apt install iftop

```

1. 通过源代码编译安装

```
## 安装比较软件包
$ sudo um install libpcap libpcap-devel ncurses ncurses-devel flex byacc

## 下载软件包
$ wget "<http://www.ex-parrot.com/~pdw/iftop/download/iftop-0.17.tar.gz>"
$ tar zxvf iftop-0.17.tar.gz
$ cd iftop-0.17
$ ./configure
$ make && make install

```

### 常用参数

```
-i 指定需要检测的网卡， 如果有多个网络接口，则需要注意网络接口的选择，如：# iftop -i eth1
-B 将输出以 byte 为单位显示网卡流量，默认是 bit
-n 将输出的主机信息都通过 IP 显示，不进行 DNS 解析
-N 只显示连接端口号，不显示端口对应的服务名称
-F 显示特定网段的网卡进出流量  如: iftop -F 192.168.85.0/24
-h 帮助，显示参数信息
-p 以混杂模式运行 iftop，此时 iftop 可以用作网络嗅探器
-P 显示主机以及端口信息
-m 设置输出界面中最上面的流量刻度最大值，流量刻度分 5 个大段显示  如：# iftop -m 100M
-f 使用筛选码选择数据包来计数  如 iftop -f filter code
-b 不显示流量图形条
-c 指定可选的配置文件，如：iftop  -c config file
-t 使用不带 ncurses 的文本界面，
    以下两个是只和 -t 一起用的：
    -s num num 秒后打印一次文本输出然后退出，-t -s 60 组合使用，表示取 60 秒网络流量输出到终端
    -L num 打印的行数
-f 参数支持 tcpdump 的语法，可以使用各种过滤条件。

```

### 界面操作

## 附录

[https://blog.csdn.net/qq_40907977/article/details/115066452](https://blog.csdn.net/qq_40907977/article/details/115066452)