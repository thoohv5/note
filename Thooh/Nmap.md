---
title: NMAP(Network Mapper)
date: 2023-12-12
tags: [笔记]
type: note
status: incomplete
---
# 概述
## NMAP
Network exploration tool and security / port scanner
网络探索工具和安全/端口扫描器

## 功能

1. interesting ports table：port number，protocol，service name, state
2. reverse DNS names
3. operating system guesses
4. device types
5. MAC addresses

### 命令
https://nmap.org/book/man-briefoptions.html

```
nmap [<Scan Type> ...] [<Options>] {<target specification>}
```


#### Scan type

```
# TCP SYN（隐形）扫描
namp -sS

# TCP 连接扫描
namp -sT

# UDP 扫描 ( `-sU` )
namp -sU

# 版本检测
namp -sV

# 启用操作系统检查
nmap -O

# 脚本引擎（NSE）
nmap -sC
nmap --script
```


#### Target Specification

IP address, hostname, CIDR, IPv6, or octet ranges

```
# 多个主机规范
nmap scanme.nmap.org 192.168.0.0/8 10.0.0,1,3-7.-

# 无DNS解析
nmap -n
```

### Port Scanning Basics
https://nmap.org/book/man-port-scanning-basics.html

- open
- closed
- filtered
- unfiltered
- open|filtered
- closed|filtered

### Timing and Performance
https://nmap.org/book/man-performance.html

# 示例

```
# 操作系统
nmap -O --osscan-guess --max-os-tries 1 --noninteractive 10.2.13.98  
nmap -6 -O --osscan-guess --max-os-tries 1 --noninteractive 2001::99  


# TCP
nmap -n -T4 -sT  10.2.13.98  
nmap -6 -n -T4 -sT 2001::99  


​# TCP & Version
nmap -n -sT -T4 -sV 10.2.13.98  
nmap -6 -n -sT -T4 -sV 2001::99  


# UDP
nmap -n -T4 -sU  10.2.13.98  
nmap -6 -n -T4 -sU 2001::99  
​  

# HTTP title​ 
nmap -n -T4 --script=http-title       10.2.13.98  
nmap -6  -n -T4 --script=http-title   2001::99  
​   
```

# 附录
https://nmap.org/book/man.html
https://nmap.org/book/port-scanning-options.html#port-scanning-options-ports