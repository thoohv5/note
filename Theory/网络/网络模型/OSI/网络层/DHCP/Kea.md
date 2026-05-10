---
title: Kea DHCP
date: 2026-04-07
tags: [理论, 网络]
type: reference
status: complete
source: https://www.cnblogs.com/studio313/p/11783822.html
---

## Kea DHCP

Kea 是 ISC 开发的**现代化 DHCP 服务器**，是经典 ISC DHCP 的继任者，采用 C++ 编写，支持高性能和模块化架构。

### 核心特性

- **模块化**：DHCPv4、DHCPv6、DDNS 独立进程
- **数据库后端**：支持 MySQL、PostgreSQL、Cassandra 存储租约
- **REST API**：提供管理控制接口
- **高性能**：每秒可处理数千个租约请求
- **HA 支持**：内置热备（hot-standby）模式

### MySQL 后端配置

```json
{
  "lease-database": {
    "type": "mysql",
    "name": "kea",
    "host": "localhost",
    "user": "kea",
    "password": "secret",
    "connect-timeout": 10
  }
}
```

### 与 ISC DHCP 对比

| 特性 | ISC DHCP | Kea |
|------|----------|-----|
| 语言 | C | C++ |
| 数据库 | 有限支持 | 原生支持 |
| API | 无 | REST API |
| 动态更新 | 需重启 | 支持热重载 |

### 适用场景

- ISP 大规模 IP 地址分配
- 企业网络 DHCP 服务
- 云环境动态 IP 管理

### 注意

- Kea 配置语法与 ISC DHCP 不兼容，迁移需重写配置。
- MySQL 后端需提前创建数据库和表结构（`kea-admin db-init`）。

### 相关

- [[DHCP]] [[DHCP failover]] [[DHCP中继]]