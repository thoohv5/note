---
title: redis rdb tools
date: 2026-04-07
  - 基础设施
  - Redis
type: note
status: incomplete
---

## redis rdb tools

[https://github.com/sripathikrishnan/redis-rdb-tools](https://github.com/sripathikrishnan/redis-rdb-tools)

## 安装

```bash
pip install rdbtools python-lzf
```

## 解析整个rdb文件

```bash
rdb -c memory xxx.rdb -f output_memory.csv
```

## **查看单个key**

```bash
redis-memory-for-key -s b.redis -p 1959 myhash
```

## 解析rdb文件成html格式

```bash
redis-profiler 
```