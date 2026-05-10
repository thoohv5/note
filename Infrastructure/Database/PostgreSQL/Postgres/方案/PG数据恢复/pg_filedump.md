---
title: pg_filedump
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: guide
status: complete
---

## pg_filedump

### 官网下载

[https://git.postgresql.org/gitweb/?p=pg_filedump.git;a=summary](https://git.postgresql.org/gitweb/?p=pg_filedump.git;a=summary)

### 安装步骤

```

git clone git://git.postgresql.org/git/pg_filedump.git
cd pg_filedump
make
make install

```

### 使用说明

```

## 基础信息
SELECT oid, datname FROM pg_database;
SELECT oid, relname FROM pg_class WHERE relkind = 'r';

## 查找对应的表文件
select pg_relation_filepath('t_dump');

## 确保数据刷到磁盘
checkpoint

## 查看信息
pg_filedump -D int,text,timestamp -i -f base/16385/25316
pg_filedump -i -f base/16385/25316

## 识别坏块
cat /tmp/t_network_ex.txt | awk '/Block/ && match($2, /^[0-9]+$/) {block=$2} /Item/ {item=$2} /Error/{printf("(%s,%s)\\n"),block,item}'|uniq|awk '{printf("%s,", $0)}'

```