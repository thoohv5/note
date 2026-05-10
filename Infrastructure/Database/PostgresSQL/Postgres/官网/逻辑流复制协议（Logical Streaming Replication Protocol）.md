---
title: 逻辑流复制协议（Logical Streaming Replication Protocol）
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## 逻辑流复制协议（Logical Streaming Replication Protocol）

### 逻辑流复制协议（Logical Streaming Replication Protocol）

启动消息流

```
START_REPLICATION SLOT slot_name LOGICAL

```

### 逻辑流复制参数（Logical Streaming Replication Parameter）

proto_version

	支持版本 1，2，3，4

	版本2 支持PG14+，允许流式传输进行大型事务

	版本3支持PG15+，允许流式传输两阶段提交

	版本4支持PG16+，允许并行应用正在进行的大型事务流

Publication_names

	需要订阅（接受更改）的发布名称的逗号分隔列表

### 逻辑复制协议消息（Logical Replication Protocol Messages）

所有顶级协议消息都以消息类型字节开头

由于流式复制协议提供消息长度，因此顶级协议消息无需在其标头中嵌入长度

### 逻辑复制协议流（Logical Replication Protocol Message Flow）

逻辑复制协议逐个发送各个事务。

Begin 和 Commit 消息之间的所有消息都属于同一事务。

Begin Prepare 和 Prepare 消息之间的所有消息都属于同一事务。

每个发送的事务都包含零条或多条 DML 消息（插入、更新、删除）。

在级联设置的情况下，它还可以包含 Origin 消息。源消息指示事务源自不同的复制节点。

由于逻辑复制协议范围内的复制节点几乎可以是任何内容，因此唯一的标识符是源名称。

下游有责任根据需要（如果需要）处理此问题。Origin 消息始终在事务中的任何 DML 消息之前发送。

每个 DML 消息都包含一个关系 OID，用于标识所操作的发布者的关系。