---
title: hoclock
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: incomplete
---

# hoclock

```
# 查看硬件时钟的时间
hwclock

# 将系统时钟的时间写入硬件时钟
hwclock --systohc/-w

# 将硬件时钟的时间同步到系统时钟
hwclock --hctosys/-s

# 设置硬件时钟的时间
hwclock --set --date="YYYY-MM-DD`

```