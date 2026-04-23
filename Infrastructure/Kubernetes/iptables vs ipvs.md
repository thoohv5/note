---
title: iptables vs ipvs
date: 2026-04-07
  - 基础设施
  - K8s
type: note
status: incomplete
---

## iptables vs ipvs

## iptables

Linux 内核 Netfilter 的规则引擎

```latex
## 路由之前
iptables -t nat -A PREROUTING \
  -d 20.20.20.20 -p tcp --dport 80 \
  -j DNAT --to-destination 192.168.1.10:8080

## 回包之前
iptables -t nat -A POSTROUTING \
  -s 192.168.1.0/24 \
  -j SNAT --to-source 20.20.20.20

Client → 20.20.20.20:80 
           Client → 192.168.1.10:8080 (DNAT)
           Client ← 192.168.1.10:8080 (SNAT)
Client ← 20.20.20.20:80 
```

## IPVS

Linux Virtual Server