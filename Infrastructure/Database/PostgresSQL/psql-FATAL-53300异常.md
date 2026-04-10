---
title: PostgreSQL 之"psql: FATAL: 53300"异常_mb5fe190725e8a3的技术博客_51CTO博客
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: complete
---

# PostgreSQL 之"psql: FATAL: 53300"异常_mb5fe190725e8a3的技术博客_51CTO博客

URL: https://blog.51cto.com/u_15064650/2884475

异常详情：

psql: FATAL:  53300: remaining connection slots are reserved for non-replication superuser connections

含义：普通用户的连接数已满，保留用于非复制的超级用户连接。

就是说数据库连接数不足了。

1、查看系统为超级用户预留的连接数。

postgres=> show superuser_reserved_connections; superuser_reserved_connections-------------------------------- 10(1 row)

2、查看配置的最大连接数。

postgres=> select setting::int8 as max_conn  from pg_settings where name = 'max_connections'; max_conn----------     2048(1 row)

3、查看当前的连接信息。

postgres=> select datname,pid,application_name,state from pg_stat_activity;       datname       |  pid  |          application_name           | state---------------------+-------+-------------------------------------+--------                     | 14682 |                                     |                     | 14687 |                                     |                     | 10223 | athena_103075390538834_14c910ad     | postgres            | 36107 | psql                                | active gitlabhq_production | 32323 | /opt/gitlab/embedded/bin/gitlab-mon | idle

4、查看数据库剩余连接数还有多少。

postgres=> select max_conn-used_conn as remaining_conn from (select setting::int8 as max_conn,(select count(*) from pg_stat_activity) as used_conn from pg_settings where name = 'max_connections') t; remaining_conn----------------            2(1 row)

5、通过pid终止空闲连接。

postgres=> select pg_terminate_backend(pid) from pg_stat_activity;

其实对于上面的情况，解决方案要么就是增加max_connections的配置，要么使用连接池通过较小的连接池路由大量的用户请求。

还有一种情况要考虑的就是：

**应用中是不是有很多未正常关闭的连接？**

比如我这次遇到的问题，就是Django的应用中，存在很多未正常关闭的连接。

Django的ORM在使用上是有一些坑的。

正常Django web应用是在程序接受到请求之后，在第一访问数据库的时候会创建一个数据库连接，直到请求结束，关闭连接。**它没有实现连接池**，所以当请求并发大的时候，就会占用大量的数据库连接。

还有一个坑就是，如果是非web项目，当不存在请求结束事件，它的连接就一直不会被关闭。比如有时候程序中会另起一些线程做异步任务，而这些线程里的数据库连接是不会自动关闭的，这时候就会导致连接泄漏，连接数一直增长。

所以像这样的情况就要找时机去**主动关闭数据库连接**，防止连接一直被占用，从而导致连接数被耗尽。

最后，pg连接数过高会导致致命的性能问题，甚至能将PG引入OOM、系统无响应等状况，所以在对待数据库连接的问题上，是一定要慎重的。在使用pg多连接时，一般需要使用pgpool或pgbouncer之类的连接池，同时还要自检应用中是否存在未正常关闭的连接。

https://mp.weixin.qq.com/s/aK_3wiMZjmhsKO0lCsSEVQ