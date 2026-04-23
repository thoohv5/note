---
title: PG关闭模式
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: complete
---

## PG关闭模式

## 命令

```
pg_ctl stop -D $PGDATA [-m shutdown-model]

```

### shutdown-model

### smart模式

![[PG关闭模式.excalidraw]]

等所有的连接中断后，断开数据库。如果客户端连接不终止，则无法关闭数据库。

- 等待备份完成
- 等待客户端连接释放

### fast模式

快速关闭数据库，断开客户端的连接，让已有的事务回滚，然后正常关闭数据库。

### immediate模式

立即关闭数据库，立即停止数据库进程，直接退出，下次启动时会进行实例恢复。

### 启动

```
pg_ctl -D datadir  -l logfile start

```

### 状态

```

pg_ctl -D datadir  -l logfile status

```