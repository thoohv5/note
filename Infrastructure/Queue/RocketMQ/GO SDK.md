---
title: GO SDK
date: 2026-04-07
tags:
  - 基础设施
  - 消息队列
type: guide
status: complete
---

## GO SDK

### **Apache RocketMQ Go 客户端完整对比**

| **对比维度** | **`rocketmq-client-go/v2` (旧版)** | **`rocketmq-clients` (新版推荐)** |
| --- | --- | --- |
| **项目状态** | 维护中（仅 Bug 修复） | 活跃开发（官方主推） |
| **协议支持** | Remoting (TCP 长连接) | gRPC (HTTP/2) |
| **兼容版本** | RocketMQ 4.x | RocketMQ 5.x+ |
| **依赖组件** | 直接连接 NameServer + Broker | 依赖 Proxy 或直接连接 Broker (支持 gRPC) |
| **服务发现** | 手动配置 NameServer 地址 | 自动路由发现 |
| **连接管理** | 需手动维护长连接 | gRPC 多路复用，自动负载均衡 |
| **语言实现** | 纯 Go | 跨语言统一接口 (C++/Go/Java 等) |
| **生产环境验证** | 广泛使用 | 逐步推广中 |

---

### **功能支持对比**

| **功能** | **`rocketmq-client-go/v2`** | **`rocketmq-clients`** | **备注** |
| --- | --- | --- | --- |
| 普通消息 | ✅ | ✅ |  |
| 顺序消息 | ✅ | ✅ |  |
| 事务消息 | ✅ | ✅ |  |
| 延迟消息 | ✅ | ✅ |  |
| 批量消息 | ✅ | ✅ |  |
| 消息过滤 (Tag/SQL92) | ✅ | ✅ |  |
| 消息轨迹 | ❌ | ✅ | 新版支持消息生产/消费全链路追踪 |
| 弹性扩缩容 | ❌ | ✅ | 新版支持 Broker 动态扩缩容 |
| 多协议互通 | ❌ | ✅ | 通过 gRPC 实现多语言互通 |
| Prometheus 监控 | ❌ | ✅ | 新版内置指标暴露 |

---

### **性能与资源对比**

| **指标** | **`rocketmq-client-go/v2`** | **`rocketmq-clients`** |
| --- | --- | --- |
| 单机吞吐量 | 高（万级 QPS） | 极高（十万级 QPS，gRPC 优化） |
| 连接数限制 | 受限于 TCP 连接数 | gRPC 多路复用，连接数需求低 |
| CPU 占用 | 低 | 中等（gRPC 编解码开销） |
| 内存占用 | 低 | 中等（协议缓冲区占用） |
| 网络延迟 | 低（直接 TCP） | 极低（HTTP/2 + 多路复用） |

---

### **开发体验对比**

| **方面** | **`rocketmq-client-go/v2`** | **`rocketmq-clients`** |
| --- | --- | --- |
| 代码简洁性 | 接口简单，但需手动管理连接 | 高度封装，自动化管理 |
| 调试支持 | 日志详细，但无内置工具 | 内置 gRPC 调试接口 + 消息轨迹 |
| 文档完整性 | 完善（但面向旧版） | 逐步完善中 |
| 社区支持 | 问题响应较慢 | 官方优先支持 |
| 迁移成本 | - | 需重写生产/消费逻辑 |

---

### **适用场景总结**

| **场景** | **推荐客户端** | **原因** |
| --- | --- | --- |
| 已有 RocketMQ 4.x 集群 | **`rocketmq-client-go/v2`** | 无需改造基础设施 |
| 需要消息轨迹等新特性 | **`rocketmq-clients`** | 仅新版支持 |
| 多语言混合环境（如 Go+Java） | **`rocketmq-clients`** | gRPC 跨语言互通 |
| 资源敏感型应用（低配服务器） | **`rocketmq-client-go/v2`** | 轻量级实现 |
| 高吞吐量需求（>10万 QPS） | **`rocketmq-clients`** | gRPC 多路复用优势 |

---

### **决策建议**

1. **新项目**：无脑选 **`rocketmq-clients`**（未来官方主推方向）
2. **历史项目**：
    - 如果无需新特性，可继续使用 **`v2`**
    - 如需消息轨迹等功能，逐步迁移到新版
3. **性能关键型应用**：实测对比（新版 gRPC 在高压下表现更优）

如果需要具体代码示例或迁移指导，可以进一步展开说明！ 🚀

## Github

### `github.com/apache/rocketmq-clients/golang/v5(推荐)`

[https://github.com/apache/rocketmq-clients](https://github.com/apache/rocketmq-clients)

### `github.com/apache/rocketmq-client-go/v2`

[https://github.com/apache/rocketmq-client-go](https://github.com/apache/rocketmq-client-go)