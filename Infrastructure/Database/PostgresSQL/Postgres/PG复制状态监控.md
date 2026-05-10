---
title: PG复制状态监控
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## PG复制状态监控

### 发布端

### 逻辑/物理复制槽信息

```
select * from pg_replication_slots;

```

| Column | Type | 说明 |
| --- | --- | --- |
| slot_name |  |  |
| plugin |  |  |
| slot_type |  |  |
| datoid |  |  |
| database |  |  |
| temporary |  |  |
| active |  |  |
| active_pid |  |  |
| xmin |  |  |
| catalog_xmin |  |  |
| restart_lsn |  | 复制槽复制需要的最老的wal位点信息，因此当数据库checkpoint期间也不会自动进行删除，除非该LSN位点落后超过max_slot_wal_keep_size |
| confirmed_flush_lsn |  | 逻辑复制已经确认订阅到的wal位点信息，在该位点之前的wal日志将不再需要，可以在主数据库checkpoint后删除 |
| wal_status |  | 复制槽状态<br />reserved : 复制需要保留的文件在max_wal_size之内<br />extended : 复制需要保留的文件已经超过了max_wal_size，无论是通过复制槽还是wal_keep_size unreserved : 复制槽不再需要保留相关的wal文件，该状态是一个临时状态，后续可返回reserved或extended <br />lost : 复制需要的wal文件已经被删除，且该复制槽变为不可用 |
| safe_wal_size |  | 复制槽状态不被置于"lost"下需要写入的wal大小 |

### 逻辑/物理复制的详细状态、位点复制信息

```
select * from pg_stat_replication;

```

| Column | Type | 说明 |
| --- | --- | --- |
| pid |  | 复制用户 |
| usesysid |  | 复制槽名称 |
| usename |  | 链接wal sender的客户端IP |
| application_name |  | 复制进程启动时间/复制进程链接wal sender时间 |
| client_addr |  |  |
| client_hostname |  |  |
| backend_start |  |  |
| backend_xmin |  |  |
| state |  | wal sender状态  <br />startup : 启动状态 <br />catchup : 逻辑/物理复制备库正在追赶primary节点（表明当前复制槽存在延迟） <br />streaming : 逻辑/物理复制备库已经追赶上primary节点，进行流复制中 <br />backup : wal sender正在发送备份文件 <br />stopping : wal sender停止工作 |
| sent_lsn |  | wal sender 发送最后一个wal位点信息 |
| write_lsn |  | 逻辑/物理复制备库已经落盘（write）的wal位点信息 |
| flush_lsn |  | 逻辑/物理复制备库已经落盘（flush）的wal位点信息 |
| replay_lsn |  | 逻辑/物理复制备库已经应用的wal位点信息 |
| write_lag |  | write延迟 |
| flush_lag |  | flush延迟 |
| replay_lag |  | replay延迟 |
| sync_priority |  | 数据同步优先级，同步复制的优先级高于异步复制 |
| sync_state |  | async : 异步复制 <br />potential : 目前是异步复制且后续有可能提升为同步复制 <br />sync : 同步复制 <br />quorum : 同步复制的备选节点 |
| reply_time |  | 逻辑/物理复制备库返回应用日志的最后时间 |

### 订阅端

### 逻辑复制订阅端每个表对象的逻辑复制状态

```
select * from pg_subscription_rel;

```

| Column | Type | 说明 |
| --- | --- | --- |
| srsubid |  | 订阅通道oid，等同于pg_subscription.oid |
| srrelid |  | 表对象oid,等同于pg_class.oid |
| srsubstate |  | 订阅状态 <br /> i : initialize 初始化 <br />d : data 正在拷贝数据 <br />s : synchronized 同步中<br /> r : ready 已就绪（复制中的常态） |
| srsublsn |  | 当订阅状态为s或者r时远程LSN位点,其他状态下该参数为NULL |

### 逻辑复制订阅端lsn接收等信息

```
select * from pg_stat_subscription;

```

| Column | Type | 说明 |
| --- | --- | --- |
| subid |  |  |
| subname |  | 订阅通道名称 |
| pid |  |  |
| relid |  |  |
| received_lsn |  | wal recever 接收到的 lsn 位点信息 |
| last_msg_send_time |  | wal sender 最近一次发送信息的时间 |
| last_msg_receipt_time |  | wal recever 最后一次接收到信息的时间 |
| latest_end_lsn |  | wal recever 汇报给 wal sender的lsn位点信息 |
| latest_end_time |  | wal recever 汇报给 wal sender 信息的时间 |

### 物理复制standby实例，复制链接信息、lsn接收等信息

```
select * from pg_stat_wal_receiver;

```

| Column | Type | 说明 |
| --- | --- | --- |
| pid |  |  |
| status |  | wal接收器状态 |
| receive_start_lsn |  | wal recever 接收到的第一个lsn位点信息 |
| receive_start_tli |  | wal recever 接收到第一个lsn位点信息的时间轴 |
| received_lsn |  | wal recever 接收到的，并且已经将wal写盘（没有flush）的最近一个的lsn位点 |
| received_tli |  | wal recever 接收到的，并且已经将wal写盘（没有flush）的最近一个的lsn位点时间轴 |
| flushed_lsn |  | wal recever 接收到的，并且已经将wal写盘，并且已经flush的最近一个的lsn位点 |
| received_tli |  | wal recever 接收到的，并且已经将wal写盘，并且已经flush的最近一个的lsn位点时间轴 |
| latest_end_lsn |  | wal recever发送给 wal sender的最后一个lsn信息 |
| latest_end_time |  | wal recever发送给 wal sender的最后一个lsn的时间 |
| slot_name |  | 复制槽名称 |
| sender_host |  | wal sender 主机IP |
| sender_port |  | wal sender 端口 |
| conninfo |  | 连接wal sender信息 |