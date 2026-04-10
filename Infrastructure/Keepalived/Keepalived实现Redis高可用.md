---
title: Keepalived实现Redis高可用
date: 2026-04-07
tags: [基础设施]
type: note
status: complete
---

# Keepalived实现Redis高可用

## 作用范围

keepalived只能做到对网络故障和keepalived本身的监控，即当出现网络故障或者keepalived本身出现问题时，进行切换。

通常情况下，利用keepalived做热备，其中一台设置为master，另一台设置为backup。当master出现异常后，backup自动切换为master。当backup成为master后，master恢复正常后会再次抢占成为master，导致不必要的主备切换。因此可以将两台keepalived初始状态均配置为backup，设置不同的优先级，优先级高的设置nopreempt解决异常恢复后再次抢占的问题。