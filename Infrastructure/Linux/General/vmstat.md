---
title: vmstat
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
source: https://blog.csdn.net/lin443514407lin/article/details/54342754
---

## vmstat

`vmstat` 是 Linux 常用的系统性能观察工具，用于查看进程、内存、交换分区、I/O、系统中断和 CPU 状态。它适合快速判断系统瓶颈方向，而不是替代 `top`、`iostat`、`sar` 等专项工具。

## 常用命令

```bash
vmstat
vmstat 1
vmstat 1 10
vmstat -s
vmstat -d
```

- `vmstat 1`：每 1 秒输出一次，适合观察趋势。
- `vmstat 1 10`：每 1 秒输出一次，共 10 次。
- `vmstat -s`：输出内存和事件统计。
- `vmstat -d`：输出磁盘统计。

## 字段说明

```text
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
```

- `r`：运行队列中的进程数，持续大于 CPU 核数说明 CPU 可能拥塞。
- `b`：不可中断睡眠进程数，通常与 I/O 等待有关。
- `swpd`：已使用 swap 大小。
- `free`、`buff`、`cache`：空闲内存、块设备缓冲、页缓存。
- `si`、`so`：swap in/out，持续非 0 说明内存压力明显。
- `bi`、`bo`：块设备读写速率。
- `in`、`cs`：中断数和上下文切换数。
- `us`、`sy`、`id`、`wa`、`st`：用户态、内核态、空闲、I/O 等待、虚拟化偷取时间。

## 排查思路

- `r` 持续偏高：结合 `top`、`pidstat` 查 CPU 热点进程。
- `wa` 持续偏高：结合 `iostat -x` 查磁盘延迟和利用率。
- `si/so` 持续非 0：说明发生频繁换页，需要检查内存占用。
- `cs` 异常升高：可能是线程过多、锁竞争或频繁系统调用。
- `b` 偏高：通常表示进程在等待 I/O 或内核资源。

## 注意事项

第一次输出通常是系统启动以来的平均值，排查实时问题时应关注后续采样行。

## 关联

- [[Linux]]
- [[top]]
- [[iostat]]
