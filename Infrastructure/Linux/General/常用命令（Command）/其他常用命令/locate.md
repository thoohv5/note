---
title: locate
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: incomplete
---

## locate

文件查找命令，不同于find在整块磁盘中搜索，locate命令是在数据库文件中查找，数据库文件系统每天通过定时任务更新，想及时手动更新**updatedb**命令。更新的是/var/lib/mlocate/mlocate.db，所使用的配置文件是/etc/updatedb.conf，可以在配置文件中定义排除掉，该命令在后台cron计划任务中定期执行。

```
updatedb

locate filename

```