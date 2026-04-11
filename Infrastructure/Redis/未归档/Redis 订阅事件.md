---
title: Redis 订阅事件
date: 2026-04-07
tags: [基础设施, Redis]
type: guide
status: complete
---

# Redis 订阅事件

# 开启键空间通知功能

## 配置

```yaml
# 文件配置
notify-keyspace-events Ex

# 动态配置
redis-cli config set notify-keyspace-events Ex
```

## 选项

- `K` 键空间事件，通过 `__keyspace@ 发布 <db>__` 前缀。
- `E` Keyevent 事件，以 `__keyevent@ 发布 <db>__` 前缀。
- `g` 通用命令（非类型特定），如 DEL、EXPIRE、RENAME 等
- `$` 字符串命令
- `l` 列表命令
- `s` 设置命令
- `h` 哈希命令
- `z` 排序集合命令
- `x` 过期事件（每次密钥过期时生成的事件）
- `e` 驱逐事件（当密钥因最大内存而被驱逐时生成的事件）
- `n` 新密钥事件（注意：不包含在“A”类中）
- `t` 流命令
- `d` 模块密钥类型事件
- `m` 密钥未命中事件（注意：不包含在“A”类中）
- `A` g$lshzxetd 的别名，因此“AKE”字符串表示所有事件（# （由于其独特性质，密钥未命中事件被排除在“A”之外）

“notify-keyspace-events”以由零个或多个字符组成的字符串作为参数。空字符串表示通知被禁用

# 订阅事件

```yaml
__keyevent@<db>__:<event> 或 __keyspace@<db>__:<key>

redis-cli psubscribe '__keyevent@0__:expired'
redis-cli psubscribe '__keyevent@0__:del'
redis-cli psubscribe '__keyspace@0__:mykey'
```

# 附录

[Redis keyspace notifications](https://redis.io/docs/latest/develop/use/keyspace-notifications/)