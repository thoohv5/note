---
title: tcpdump
date: 2026-04-07
tags: [软件工具, Wireshark]
type: note
status: complete
---

## tcpdump

```bash
-c : 抓包次数，打到指定次数后停止抓包
-C : 文件大小，写入文件钱判断文件是否超过了设置的大小，超过了则关闭当前文件，开启新文件
-i : 指定监听的网络接口
-nn : 不进行端口名称的转换
-r : 从指定文件中读取包
-s : 设置每个数据包的大小
-t : 在输出的每一行不打印时间戳
vv : 输出详细的报文信息
-w : 保存到指定文件中而不是打印出来，如tcpdump -w /home/test.pcap

```

### 过滤方式

| 过滤项 | 过滤项说明 | 示例 |
| --- | --- | --- |
| host、src host、dst host | 过滤主机 | tcpdump host 192.168.11.11 |
| port，src port，dst port | 过滤端口 | tcpdump port 19999 |
| ip，ip6，arp，tcp，udp，icmp | 过滤协议 | tcpdump udp 192.168.11.11 |
| and，or，not | 逻辑表达式 | tcpdump host 192.168.11.11 and port 19999 |

```go
tcpdump -i eth0 host 192.168.11.11 -w /root/test.pcap

tcpdump -i eth0 src host 192.168.11.11 and dst host 192.168.11.11 -w /root/test.pcap

```