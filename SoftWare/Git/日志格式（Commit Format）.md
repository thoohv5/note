---
title: 日志格式（Commit Format）
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: note
status: complete
---

## 日志格式（Commit Format）

## 注释格式化

### 规范

```
<header>(<type>[<scope>]: <subject>)
// 空一行

<body>
// 空一行

<footer>
```

### 说明

```
<header>

<type>
feat 新功能（feature）
fix 修补bug
docs 文档（documentation）
style 格式（不影响代码运行的变动）
refactor 重构（即不是新增功能，也不是修改bug的代码变动）
test 增加测试
chore 构建过程、辅助工具的变动
perf 提高性能

<scope>
影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

<subject>
目的的简短描述，不超过50个字符。 以动词开头，使用第一人称现在时，比如 change，而不是 changed 或 changes 第一个字母大写 结尾不加句号

<body>
解决问题的方案 解决问题的影响点

<footer>
不兼容变动 关闭Issue

```

## 附录

[https://ruby-china.org/topics/15737](https://ruby-china.org/topics/15737)[https://blog.51cto.com/u_14637492/2897168](https://blog.51cto.com/u_14637492/2897168)