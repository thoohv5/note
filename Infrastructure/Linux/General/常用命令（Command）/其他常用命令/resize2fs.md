---
title: resize2fs
date: 2026-04-07
tags: [基础设施, Linux]
type: reference
status: incomplete
---

# resize2fs

# 概述

resize2fs,resize to filesystem,用于同步文件系统容量到内核

# 命令

```

resize2fs [参数] 设备

```

| 参数 | 作用 |
| --- | --- |
| -d | 打开调试特性 |
| -p | 打印已完成的百分比进度条 |
| -f | 强制执行调整大小操作 |
| -F | 刷新文件系统设备的缓冲区 |