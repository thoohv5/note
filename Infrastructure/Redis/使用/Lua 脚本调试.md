---
title: Lua 脚本调试
date: 2026-04-07
tags: [基础设施, Redis, 使用]
type: reference
status: complete
---

## Lua 脚本调试

## 调试

```bash
  --ldb              Used with --eval enable the Redis Lua debugger.
  --ldb-sync-mode    Like --ldb but uses the synchronous Lua debugger, in
```

在调试器里可以用命令：

- `n` / `next` → 单步执行
- `p` / `print <var>` → 打印变量
- `c` / `continue` → 继续执行到结束
- `abort` → 退出调试

## 日志

```lua

redis.log(redis.LOG_DEBUG, "example text")
```

### 用法说明

- **函数**：`redis.log(level, message)`
- **level**（日志等级）：
    - `redis.LOG_DEBUG` → 调试信息
    - `redis.LOG_VERBOSE` → 冗长信息
    - `redis.LOG_NOTICE` → 普通通知
    - `redis.LOG_WARNING` → 警告
- **message**：你要打印的字符串

## 错误

在 **Redis Lua 脚本**里，`error()` 和 `redis.error_reply()` 都能让脚本返回错误，但 **用途和行为略有区别**：

---

### 1️⃣ `error()`

```lua
error("Something went wrong")

```

- **Lua 内置函数**，Redis Lua 直接抛出 Lua 错误。
- Redis 返回给客户端的错误信息格式类似：

```
(error) ERR Something went wrong

```

- **特点**：
    - 会 **终止脚本执行**。
    - 常用于 **程序逻辑错误或异常中断**。
    - 错误信息会带上 `ERR` 前缀。

---

### 2️⃣ `redis.error_reply()`

```lua
return redis.error_reply("Something went wrong")

```

- **Redis 提供的接口**，返回标准 **Redis 错误回复**。
- 客户端看到的格式：

```
(error) Something went wrong

```

- **特点**：
    - 更“Redis 风格”，不会带 `ERR` 前缀。
    - 适合 **业务层错误**，例如参数校验失败、Key 不存在。
    - 可以和 `return` 一起使用，不会中断 Lua 调试器（可继续调试其他分支逻辑）。

---

### 3️⃣ 简单对比

| 特性 | `error()` | `redis.error_reply()` |
| --- | --- | --- |
| 来源 | Lua 内置 | Redis Lua API |
| 脚本终止 | 会终止 | 可用 `return` 返回，不立即终止 |
| 错误前缀 | ERR | 无 ERR |
| 用途 | 逻辑/异常错误 | 业务/参数/操作错误 |
| 调试友好 | 会停止执行 | 可以在调试器里继续调试其他逻辑 |