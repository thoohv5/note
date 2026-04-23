---
title: OpenTelemetry Go
date: 2026-04-07
  - 微服务
  - 服务治理
type: reference
status: complete
---

## OpenTelemetry Go

### **自动埋点支持**

| **模块路径** | **框架/库** | **功能描述** |
| --- | --- | --- |
| **`instrumentation/github.com/gin-gonic/gin`** | Gin Web 框架 | 自动追踪 HTTP 请求，记录路由、状态码、耗时 |
| **`instrumentation/go.mongodb.org/mongo-driver`** | MongoDB 驱动 | 捕获查询语句、执行时间、错误信息 |
| **`instrumentation/google.golang.org/grpc`** | gRPC | 跨服务的分布式追踪（Client/Server 双向集成） |
| **`instrumentation/net/http`** | 标准库 **`net/http`** | 为原生 HTTP 服务/客户端添加追踪 |

### **检测工具（Detectors）**

| **模块路径** | **功能** |
| --- | --- |
| **`detectors/aws`** | 自动检测 AWS 资源信息（EC2 实例ID、Region 等） |
| **`detectors/gcp`** | 获取 GCP 环境元数据（项目ID、虚拟机属性等） |
| **`detectors/kubernetes`** | 提取 K8s Pod/Node 信息，增强资源标签 |

### **导出器与协议扩展**

| **模块路径** | **功能** |
| --- | --- |
| **`exporters/metric/cortex`** | 将指标导出到 Cortex/Prometheus |
| **`exporters/trace/jaeger`** | Jaeger 导出器（兼容 Thrift/HTTP 协议） |
| **`propagators/b3`** | 支持 Uber 的 B3 传播协议（用于跨服务上下文传递） |

### **实用工具**

| **模块路径** | **功能** |
| --- | --- |
| **`bridge/opencensus`** | 兼容 OpenCensus 的 API 和导出器（平滑迁移） |
| **`samplers/aws/xray`** | AWS X-Ray 兼容的采样策略 |
| **`instrumentation/host`** | 采集主机指标（CPU/内存/磁盘等） |