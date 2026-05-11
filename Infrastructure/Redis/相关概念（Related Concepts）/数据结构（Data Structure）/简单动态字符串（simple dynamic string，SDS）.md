---
title: 简单动态字符串（simple dynamic string，SDS）
date: 2026-04-11
tags: [基础设施, Redis, 相关概念]
type: note
status: complete
source: https://book-redis-design.netlify.app/
---
## 简单动态字符串（SDS）

> Redis 没有直接使用 C 语言传统的字符串（以空字符结尾的字符数组），而是自己构建了一种名为简单动态字符串的抽象类型。

### 数据结构

```c
struct sdshdr {
    int len;    // 已使用字节数
    int free;   // 未使用字节数
    char buf[]; // 字节数组
};
```

### 与 C 字符串的区别

| 特性 | C 字符串 | SDS |
|------|---------|-----|
| 获取长度 | O(N) | O(1) |
| 缓冲区溢出 | 可能 | 自动扩容 |
| 内存分配 | N 次修改 N 次 | 预分配，最多 N 次 |
| 二进制安全 | 否（遇 `\0` 截断） | 是 |
| 复用 C 函数 | 直接复用 | 需调用 SDS API |

### 参考

- [简单动态字符串](https://book-redis-design.netlify.app/#/doc/02-%E7%AE%80%E5%8D%95%E5%8A%A8%E6%80%81%E5%AD%97%E7%AC%A6%E4%B8%B2)
- [[Redis 数据结构]]
- [[Redis 数据类型]]