---
title: URI vs URL vs URN
date: 2026-04-07
tags:
  - 理论
  - 网络
type: note
status: complete
---

## URI vs URL vs URN

### 统一资源**标志符**(Uniform Resource **Identifier, URI**)

```latex
urn:isbn:978-7-121-15535-2
mailto:admin@example.com
uuid:550e8400-e29b-41d4-a716-446655440000
```

### 统一资源**定位器**(Uniform Resource **Locator, URL**)

```latex
## 标准
scheme://user:pass@host:port/path?query#fragment

## 示例
**https://api.example.com/v1/users?id=1
ftp://ftp.example.com/pub/file.txt
jdbc:mysql://localhost:3306/test**
```

### 统一资源命名(Uniform Resource Name, URN)

### 关系

URI
├── URL  ← 最常用
└── URN  ← 只命名，不定位