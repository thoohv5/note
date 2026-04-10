---
title: 避免Big Key
date: 2026-04-07
tags: [基础设施, Redis]
type: note
status: complete
---

# 避免Big Key

什么是 `Big Key`：我们将含有较大数据或含有大量成员、列表数的Key称之为大Key。

- 一个 STRING 类型的 Key，它的值为 5MB（数据过大）
- 一个 LIST 类型的 Key，它的列表数量为 20000 个（列表数量过多）
- 一个 ZSET 类型的 Key，它的成员数量为 10000 个（成员数量过多）
- 一个 HASH 格式的 Key，它的成员数量虽然只有 1000 个但这些成员的 value 总大小为 100MB（成员体积过大）

### 何发现 Big Key

- 使用 redis-cli 客户端的命令 –bigkeys;
- 生成 rdb 文件，离线分析 rdb 文件。比如：redis-rdb-cli，rdbtools;
- 通过 scan 命令，对扫描出来的key进行类型判断，例如：string长度大于10K，list长度大于10240认为是big bigkeys;

### Big Key 如何删除

直接使用 DEL 命令会发生什么？危险：同步删除 bigkey 会阻塞 Redis 其他命令，造成 Redis 阻塞。
推荐使用 UNLINK 命令，异步删除 bigkey，不影响主线程执行其他命令。
在业务的低峰期使用 scan 命令查找 big key，对于类型为集合的key，可以使用脚本逐一删除里面的元素。