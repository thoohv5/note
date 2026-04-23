---
title: WithNsResolver WithNameServer WithNameServerDomain
date: 2026-04-07
  - 基础设施
  - 消息队列
type: note
status: complete
---

## WithNsResolver WithNameServer WithNameServerDomain

`WithNsResolver`、`WithNameServer`、`WithNameServerDomain` 是 RocketMQ Go SDK 中用于配置 NameServer 地址的方式，它们的作用类似，但适用场景不同。下面是它们的详细区别总结：

---

### 🔹 `WithNameServer([]string{"ip:port"})`

- **功能**：直接指定 NameServer 的地址列表。
- **使用场景**：你已经知道具体的 IP 和端口，比如：
    
    ```go
    producer, _ := rocketmq.NewProducer(
        producer.WithNameServer([]string{"192.168.7.151:9876"}),
    )
    ```
    

---

### 🔹 `WithNameServerDomain("http://example.com/namesrv")`

- **功能**：通过指定一个域名地址，从远程服务拉取 NameServer 列表（通常是负载均衡或 DNS 解析服务）。
- **使用场景**：动态获取多个 NameServer，或者用于云上的 NameServer 服务，比如阿里云的 RocketMQ 服务。
- **底层机制**：会定时访问这个域名，自动解析出最新的 NameServer 地址列表。

---

### 🔹 `WithNsResolver(resolver NsResolver)`

- **功能**：自定义 NameServer 地址的解析器（Resolver）。
- **使用场景**：你需要动态解析 NameServer 地址，例如从配置中心、数据库等获取。
- **示例**：
    
    ```go
    type MyResolver struct{}
    
    func (r *MyResolver) Resolve() []string {
        return []string{"192.168.7.151:9876"} // 可从数据库/配置中心取
    }
    
    producer, _ := rocketmq.NewProducer(
        producer.WithNsResolver(&MyResolver{}),
    )
    ```
    

---

### ✅ 总结对比：

| 方法 | 配置方式 | 动态 | 适用场景 |
| --- | --- | --- | --- |
| `WithNameServer` | 手动配置 IP | ❌ | 本地测试、固定环境 |
| `WithNameServerDomain` | 域名动态获取 IP 列表 | ✅ | 云服务 / 负载均衡 |
| `WithNsResolver` | 完全自定义逻辑 | ✅ | 配置中心 / 数据库 / 本地缓存等场景 |