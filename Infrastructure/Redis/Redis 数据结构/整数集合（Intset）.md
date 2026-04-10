---
title: 整数集合（Intset）
date: 2026-04-07
tags: [基础设施, Redis]
type: reference
status: incomplete
---

# 整数集合（Intset）

# 概述

用于保存整数值的集合抽象数据结构,它可以保存类型为int16_t、int32_t或者int64_t的整数值,并且保证集合中不会出现重复元素。

# 应用

集合（只包含整数数值元素，并且集合元素数量不多）