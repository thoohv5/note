---
title: PostgreSQL数据库连接数查询
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: guide
status: complete
---

## PostgreSQL数据库连接数查询

## 命令

```sql
## 显示系统允许的最大连接数
show max_connections;

## 显示系统保留的用户数
show superuser_reserved_connections;

## 查询当前所有连接的状态
select datname,pid,application_name,state from pg_stat_activity;

## 查看数据库剩余连接数
select max_conn-now_conn as resi_conn from (select setting::int8 as max_conn,(select count(*) from pg_stat_activity) as now_conn from pg_settings where name = 'max_connections') t;

## 按照用户分组查看
select usename, count(*) from pg_stat_activity group by usename order by count(*) desc;

## 结束连接的进程
SELECT pg_terminate_backend(15278);

## 杀死所有 idle 的进程
select pg_terminate_backend(pid) from pg_stat_activity where state=’idle’;
```

## 问题

### 使用 psql 无法连接数据库，并报错 FATAL:53300

psql: FATAL: 53300: remaining connection slots are reserved for non-replication superuser connections

普通用户的连接已满，保留用于非复制的超级用户连接。

由于连接已满，可以关闭空闲的连接

1. 查询当前所有连接的状态
2. 关闭当前 state 为 idle 空闲状态的连接

### psql: FATAL: 53300: sorry, too many clients already

数据库连接已满，无法建立新的连接。

1. 关闭空闲连接

## 附录

[postgresql 数据库连接数查询_postgresql 连接数-CSDN博客](https://blog.csdn.net/WTUDAN/article/details/122556127)