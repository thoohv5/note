---
title: pg_cron
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: guide
status: complete
---

## pg_cron

要在PostgreSQL启动时启动pg_cron后台工作程序，你需要把pg_cron添加到shared_preload_libraries参数中。如果是备库，pg_cron不会运行任何作业，但是备库提升为主库后它将自动启动。

### 安装

```sql
wget https://github.com/citusdata/pg_cron/archive/v1.3.0.tar.gz
tar zxf v1.3.0.tar.gz
cd pg_cron-1.3.0
make
make install
```

### 配置

```sql
## vi postgresql.conf:
shared_preload_libraries = 'pg_cron'
## database defaults to postgres
cron.database_name = 'postgres'
## timezone defaults to GMT
cron.timezone = 'PRC'
## log default on
cron.log_run = 'off'
## 动态后台工作
cron.use_background_workers = on
## 默认为8个
max_worker_processes = 16

## 创建扩展，需超级用户
CREATE EXTENSION pg_cron;
## 授权
GRANT USAGE ON SCHEMA cron TO postgres;

## 日志
SHOW cron.log_run;
```

```sql
## 创建定时任务
SELECT cron.schedule('30 6 * * *', 'VACUUM test');
SELECT cron.schedule('process-new-events', '* * * * *', 'CALL test()');
SELECT cron.schedule('upgrade-pgcron', '@reboot', 'ALTER EXTENSION pg_cron UPDATE');
SELECT cron.schedule_in_database('weekly-vacuum', '0 4 * * 0', 'VACUUM', 'some_other_database');

## 更新定时任务
SELECT cron.schedule('process-new-events', '0 0 * * 0', 'CALL test()');

## 删除定时任务
SELECT cron.unschedule('process-new-events');
SELECT cron.unschedule(7);

## 查看定时任务
TABLE cron.job;
SELECT * FROM cron.job;
SELECT * FROM cron.job_run_details;

```

## 附录

[https://github.com/citusdata/pg_cron](https://github.com/citusdata/pg_cron)