---
title: vmstat
date: 2026-04-07
  - 基础设施
  - Linux
type: guide
status: incomplete
---

## vmstat

### 使用率

```
vmstat 1 2|tail -n 1|awk '{print 100 -($15+$16)}'

```