---
title: pt-online-schema-change
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## pt-online-schema-change

### 流程

- 新建一张临时表 `mydb._users_new`，进行结构变更。
- 在原表 `users` 上加触发器，保证数据同步`_users_new`。
- 后台 `INSERT ... SELECT` 把老数据复制过去。
- 暂时锁表，重命名：
    
    ```sql
    RENAME TABLE users TO _users_old, _users_new TO users;
    ```
    
- 删除 `users_old`（或你可以保留）。

### 具体步骤

```sql
pt-online-schema-change \
--user=dba_user \
--password=xxx \
--host=127.0.0.1 \
D=order_db,t=orders \
--alter "ADD COLUMN refund_amount DECIMAL(10,2) DEFAULT 0.00 COMMENT '退款金额'" \
--no-version-check \
--critical-load Threads_running=50 \
--chunk-size=2000 \
--max-lag=5 \
--execute

./pt-online-schema-change \
--user=root \
--password=password \
--host=127.0.0.1 \
D=demo,t=user \
--alter "ADD COLUMN refund_amount DECIMAL(10,2) DEFAULT 0.00 COMMENT '退款金额'" \
--no-version-check \
--critical-load Threads_running=50 \
--chunk-size=2000 \
--max-lag=5 \
--execute
```

**参数：**

`--chunk-size`每批迁移行数（建议2000-5000）

`--max-lag`主从延迟阈值（秒），超时自动暂停

`--critical-load`服务器负载阈值（如Threads_running>80暂停）

## 附录

[https://github.com/percona/percona-toolkit](https://github.com/percona/percona-toolkit)

[Percona Toolkit Documentation](https://docs.percona.com/percona-toolkit/?_gl=1*ai67td*_ga*NTcwMTcxNTA5LjE3NTc0NzUzNDU.*_ga_DXWV0B7PSN*czE3NTc0NzUzNDUkbzEkZzEkdDE3NTc0NzU0MTYkajU5JGwwJGgw)

### 下载连接

[Percona Toolkit Documentation](https://docs.percona.com/percona-toolkit/pt-online-schema-change.html#downloading)

### 数据迁移脚本

```bash
-- ==============================
-- 配置参数
-- ==============================
SET @batch_size = 1000;       -- 每批迁移条数
SET @last_id = 0;             -- 上一次迁移的最大ID
SET @max_id = (SELECT MAX(id) FROM source_table);  -- 数据迁移截止ID

-- ==============================
-- 循环分批迁移
-- ==============================
WHILE @last_id < @max_id DO

  -- 插入一批数据
  INSERT INTO target_table (id, col1, col2, ...)
  SELECT id, col1, col2, ...
  FROM source_table
  WHERE id > @last_id
  ORDER BY id ASC
  LIMIT @batch_size;

  -- 更新 offset
  SET @last_id = (SELECT COALESCE(MAX(id), @last_id) FROM target_table);

  -- 可选：短暂休眠，控制负载
  DO SLEEP(0.1);

END WHILE;
```

### 可执行docker image

```sql
docker run --rm -it fr3nd/percona-toolkit bash
```