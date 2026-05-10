---
title: nc
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
---

## nc

```bash
yum install nc -y
```

```bash
-h 在线帮助。
-i<延迟秒数> 设置时间间隔，以便传送信息及扫描通信端口。
-l 使用监听模式，管控传入的资料。
-n 直接使用IP地址，而不通过域名服务器。
-o<输出文件> 指定文件名称，把往来传输的数据以16进制字码倾倒成该文件保存。
-p<通信端口> 设置本地主机使用的通信端口。
-r 随机指定本地与远端主机的通信端口。
-s<来源位址> 设置本地主机送出数据包的IP地址。
-u 使用UDP传输协议。
-v 显示指令执行过程。
-w<超时秒数> 设置等待连线的时间。
-z 使用0输入/输出模式，只在扫描通信端口时使用
```

### CentOS6

```bash
## 扫描TCP端口，端口范围是 20-2000
nc -rz  -w 2 10.252.32.122 20-2000

## 扫描UDP端口，端口范围是 20-2000
nc -z -u -w 2 10.252.32.122 20-2000
```

### CentOS7

```bash
nc -w 1   IP地址   端口 < /dev/null && echo "tcp port ok"
```

<aside>
💡 看到centos 6中nc 原有的 -r,-z选项在centos 7中都已经没有

</aside>