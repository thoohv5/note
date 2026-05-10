---
title: case when 语句
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: incomplete
---

## case when 语句

### 语法一

```bash
case 
    when 表达式1 then 结果1
    when 表达式2 then 结果2
    else 结果n
end
```

### 语法二

```bash
case 表达式
    when 匹配1 then 结果1
    when 匹配2 then 结果2
    else 结果n
end
```