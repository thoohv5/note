---
title: NTP服务
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: complete
---

## NTP服务

## ntp服务

### 同步不同机器的时间

```bash
yum install ntp -y
```

ntpd有一个自我保护设置: 如果本机与上源时间相差太大, ntpd不运行. 所以新设置的时间服务器一定要先ntpdate从上源取得时间初值, 然后启动ntpd服务。ntpd服务运行后, 先是每64秒与上源服务器同步一次, 根据每次同步时测得的误差值经复杂计算逐步调整自己的时间, 随着误差减小, 逐步增加同步的间隔. 每次跳动, 都会重复这个调整的过程.

### 同步网络时间

```bash
yum install ntpdate -y
```

### 常用服务器

```bash
中国国家授时中心：210.72.145.44
NTP服务器(上海) ：ntp.api.bz
美国：time.nist.gov
复旦：ntp.fudan.edu.cn
微软公司授时主机(美国) ：time.windows.com
台警大授时中心(台湾)：asia.pool.ntp.org

ntpdate ntp1.aliyun.com
ntpdate ntp.aliyun.com
```