---
title: Redis IP封禁
date: 2026-04-07
tags:
  - 基础设施
  - Redis
  - 运维管理
type: note
status: complete
source: https://mp.weixin.qq.com/s/d73Qz63PVKAiE5LDnVcuIg
---

## Redis IP封禁

基于 **Nginx + Lua + Redis** 实现动态 IP 黑名单，用于封禁爬虫或恶意用户请求。

### 架构原理

- **Nginx**：接入层，通过 `access_by_lua_file` 调用 Lua 脚本
- **OpenResty Lua**：检查 Redis 中的黑名单 key，按频率统计封禁
- **Redis**：存储访问计数和黑名单标记，支持过期时间

### 核心逻辑

```lua
local cliendIp = getIp()
local incrKey = "limit:count:" .. cliendIp
local blockKey = "limit:block:" .. cliendIp

-- 检查是否在黑名单中
local is_block = client:get(blockKey)
if tonumber(is_block) == 1 then
    ngx.exit(ngx.HTTP_FORBIDDEN)
end

-- 频率统计
local ip_count = client:incr(incrKey)
if tonumber(ip_count) == 1 then
    client:expire(incrKey, ip_time_out)  -- 统计窗口
end

-- 超过阈值则加入黑名单
if tonumber(ip_count) > tonumber(ip_max_count) then
    client:set(blockKey, 1)
    client:expire(blockKey, ip_block_time)  -- 封禁时长
end
```

### 配置要点

- **统计窗口**：`ip_time_out`（如 1 秒）
- **阈值**：`ip_max_count`（如 3 次）
- **封禁时长**：`ip_block_time`（如 120 秒）
- **IP 获取**：优先 `X-Real-IP` → `x_forwarded_for` → `remote_addr`
- **连接复用**：仅首次连接做 Redis auth 验证

### 优点

- 配置轻量，对服务性能影响小
- 多台 Nginx 共享 Redis 实现分布式黑名单
- 支持动态调整阈值和封禁时长

### 应用场景

| 场景 | 说明 |
|------|------|
| 防暴力破解 | 限制登录接口频率 |
| 防爬虫 | 按 IP 统计请求密度 |
| 防 DDoS | 瞬时高频 IP 自动封禁 |

### 相关笔记

- [[Nginx请求处理流程]]
- [[Redis 慢查询]]
- [[Redis 错误]]

> 参考：[Nginx+Lua+Redis 动态封禁 IP](https://mp.weixin.qq.com/s/d73Qz63PVKAiE5LDnVcuIg)