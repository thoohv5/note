---
title: 快速列表（QuickList）
date: 2026-04-07
tags: [基础设施, Redis]
type: reference
status: incomplete
---

# 快速列表（QuickList）

# 概述

quicklist 实际上是 zipList 和 linkedList 的混合体，它将 linkedList 按段切分，每一段使用 zipList 来紧凑存储，多个 zipList 之间使用双向指针串接起来。