---
title: pgsql 报错 in a read-only transaction_ORACLE的技术博客_51CTO博客
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## pgsql 报错 in a read-only transaction_ORACLE的技术博客_51CTO博客

[https://blog.51cto.com/2012ivan/2605440](https://blog.51cto.com/2012ivan/2605440)

PG cannot execute 一些dml,ddl语句的时候报：in a read-only transaction

这种情况有可能是以下原因引起的：

1 可能发生了主备切换：查看/data/pg_hba.conf

select pg_is_in_recovery(); 备机返回T

2 show default_transaction_read_only 查看是否参数值为ON

会话级别： set default_transaction_read_only = off;

DB级别:

alter database xxxxx set default_transaction_read_only = off;

系统级别：

alter system set default_transaction_read_only=off;

select pg_reload_conf();

一些参数(其实是看pg_settings 的context字段)，是可以不用重启postgres cluster 就可以生效

pg_ctl -D /var/lib/pgsql/data reload

select pg_reload_conf()

internal：这类参数为只读参数。有的是postgres程序写死的，有些是在安装数据库时intdb时设置好的。

postmaster：这类参数需要重启数据库才能生效。

sighup：不需要重启数据库，但要向postmaster进程发送sighup信号，即需要pg_ctl reload命令。

backend：无需重启数据库，只需向postmaster进程发送sighup信号。但新的配置值只能在之后的新连接中生效，已有连接中这些参数值不会改变。

superuser：这类参数可以由超级用户使用set修改。参数设置后只会影响超级用户自身的session配置，不会影响其他用户。

user：普通用户使用set设置，这类参数修改后和superuser类参数一样，也是只影响自身session。仅当没有通过set 设置会话本地值时，postgresql.conf中的改变才会影响现有的会话