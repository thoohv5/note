---
title: DTM
date: 2026-04-07
tags:
  - 微服务
  - 分布式
  - 分布式事务
type: reference
status: complete
source: https://github.com/dtm-labs/dtm
---

## DTM

### 概述

DTM 是一个跨语言分布式事务管理器，用于解决微服务、跨数据库、跨存储场景下的数据一致性问题。它把分布式事务的协调逻辑从业务服务中抽离出来，由 DTM Server 负责推进全局事务状态，业务服务只需要实现正向接口和必要的补偿/确认/回滚接口。

### 核心能力

- 支持多种事务模式：SAGA、TCC、XA、二阶段消息。
- 支持多语言 SDK：Go、Java、PHP、C#、Python、Node.js 等。
- 支持多种数据库和存储：MySQL、Redis、MongoDB、PostgreSQL、TDSQL 等。
- 支持多种微服务框架接入，例如 go-zero、Kratos、PolarisMesh。
- 支持高可用部署和水平扩展。

### 适用场景

- 跨服务订单、库存、账户余额等强一致或最终一致流程。
- 秒杀扣库存等需要协调 Redis 与数据库状态的场景。
- 缓存与数据库更新一致性控制。
- 事件发布/订阅、Outbox 模式替代方案。

### 常见模式

#### SAGA

SAGA 将一个全局事务拆成多个本地事务分支。每个分支有一个正向操作和一个补偿操作：

- 所有正向操作成功时，全局事务成功。
- 某个正向操作失败时，DTM 反向调用已经成功分支的补偿操作。
- 适合长事务和最终一致性场景。

#### TCC

TCC 将业务拆成 `Try`、`Confirm`、`Cancel` 三个阶段：

- `Try`：预留资源。
- `Confirm`：确认提交。
- `Cancel`：取消并释放资源。

TCC 对业务侵入较高，但一致性语义更清晰，适合账户资金、库存冻结等场景。

#### XA

XA 基于数据库事务协议，由资源管理器参与两阶段提交。适合数据库支持 XA 且希望减少业务补偿逻辑的场景。

#### 二阶段消息

二阶段消息用于保证本地事务与消息投递的一致性，比本地消息表方案更轻量，但依赖业务按 DTM 的消息模式接入。

### 快速理解

以跨行转账为例：

1. 业务方创建一个全局 SAGA 事务。
2. 添加 `TransOut` 转出分支及其补偿接口 `TransOutCom`。
3. 添加 `TransIn` 转入分支及其补偿接口 `TransInCom`。
4. 提交事务后，DTM 依次调用分支接口。
5. 如果转入失败，DTM 调用已成功分支的补偿接口，使余额恢复到事务前状态。

### 使用注意

- 正向接口和补偿接口都要保证幂等。
- 补偿接口要能处理“空补偿”，即正向操作未真正执行但补偿被调用的情况。
- 分支事务接口需要明确失败语义，避免超时、网络失败和业务失败混淆。
- 分布式事务不是性能优化手段，应优先控制事务边界，只在确有跨服务一致性需求时使用。

### 附录

- [DTM GitHub](https://github.com/dtm-labs/dtm)
- [DTM 中文 README](https://github.com/dtm-labs/dtm/blob/main/helper/README-cn.md)
- [[Microservices/分布式中间件/分布式事务/TCC（Try-Confirm-Cancel）|TCC]]
- [[Microservices/分布式中间件/分布式事务/可靠消息最终一致性（异步确保型 ）|可靠消息最终一致性]]
