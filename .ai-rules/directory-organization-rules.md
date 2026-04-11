---
title: 目录组织规则
date: 2026-04-11
tags:
  - 规范
  - vault
aliases:
  - 目录规范
  - 文件夹规则
cssclasses:
  - rule-document
---

# 目录组织规则

> 本文档是 Obsidian Vault 目录结构的权威参考。所有新建、迁移、重组笔记目录时必须遵守。

## 1 目录结构原则

### 原则一：主题优先，而非内容类型优先

目录按**知识主题**组织，不按**内容形式**组织。

```
✅ Language/Golang/并发/sync-Map.md
✅ Language/Golang/并发/协程.md
❌ Language/Golang/知识点/进程 & 线程 & 协程.md
❌ Language/Golang/梳理/语法/协程（goroutine）.md
❌ Language/Golang/文档（document）/Go 语言圣经/第5章 函数/函数(function)/内置函数.md
```

"知识点""梳理""文档"是内容类型分类，不是主题分类。同一主题的内容应聚合在同一目录下，无论它是知识点、梳理还是文档翻译。

### 原则二：最大深度 4 层

从 vault 根目录算起，目录层级不超过 4 层：

```
L1/  领域层    Infrastructure/
L2/  主题层    Infrastructure/Database/
L3/  子主题层  Infrastructure/Database/MySQL/
L4/  细节层    Infrastructure/Database/MySQL/锁/
```

超过 4 层的目录必须扁平化。用文件名承载细节，不要用嵌套目录。

```
❌ Infrastructure/Database/MySQL/锁（Lock）/锁粒度（Lock Granularity）/行级锁（Row Lock）/临键锁（Next-Key Lock）/
✅ Infrastructure/Database/MySQL/锁/临键锁.md
```

### 原则三：一个概念一个位置

同一概念只出现在一个目录。禁止重复目录名、同义目录。

```
❌ Algorithm/设计模式/设计模式/访问者模式（Vistor）/
✅ Algorithm/设计模式/访问者模式.md
```

### 原则四：深度超过价值时扁平化

如果一个目录只有 1-2 个文件，不要为它创建子目录层级。用文件名代替目录嵌套。

```
❌ Microservices/分布式中间件/权限系统（Permission System）/权限模型/RBAC模型/RBAC模型/数据库设计/
✅ Microservices/分布式中间件/权限系统/RBAC数据库设计.md
```

## 2 层级规则

### L1 领域层

Vault 根目录下的第一级目录，代表**知识领域**。

| 目录 | 含义 | 规则 |
|------|------|------|
| `Infrastructure/` | 基础设施 | Docker, K8s, Linux, Database, Nginx, Redis, Queue, CDC, Cloud |
| `Language/` | 编程语言 | 每种语言一个子目录 |
| `Algorithm/` | 算法与数据结构 | 设计模式、leetcode、数据结构、AI |
| `Theory/` | 理论知识 | 网络、加密、安全、AI、并发、CAP |
| `Microservices/` | 微服务 | 服务治理、分布式中间件、DevOps |
| `SoftWare/` | 软件工具 | Git, Zsh, Wireshark, CURL |
| `Measurement/` | 行测资料 | 言语理解、资料分析、判断推理 |
| `Other/` | 其他 | 项目管理、历史归档 |
| `WebClipper/` | 网页收集 | Web Clipper 保存的内容 |

**L1 命名规则**：使用中文名或英文通用名，不混用。当前 L1 目录保持不变。

### L2 主题层

每个领域下的**核心主题**。

```
Infrastructure/Database/     ← 数据库（主题）
Infrastructure/Docker/      ← 容器（主题）
Infrastructure/Linux/        ← 操作系统（主题）
Language/Golang/             ← Go 语言（主题）
Algorithm/设计模式/           ← 设计模式（主题）
```

**L2 命名规则**：使用简洁的中文或英文专有名词，不加括号翻译。

```
❌ Microservices/分布式中间件/分布式锁（Distributed Lock）/
✅ Microservices/分布式中间件/分布式锁/
```

### L3 子主题层

主题下的**细分领域**。

```
Infrastructure/Database/MySQL/     ← MySQL（子主题）
Infrastructure/Database/Redis/    ← Redis（子主题）
Language/Golang/并发/              ← 并发（子主题）
Language/Golang/第三方库/          ← 第三方库（子主题）
```

**L3 命名规则**：使用具体的技术名或概念名，避免泛化名称。

```
❌ Infrastructure/Database/MySQL/知识点/
✅ Infrastructure/Database/MySQL/事务/
```

### L4 细节层

最深层级，用**文件名**承载具体内容，不再创建子目录。

```
✅ Infrastructure/Database/MySQL/锁/临键锁.md
✅ Infrastructure/Database/MySQL/锁/意向锁.md
✅ Infrastructure/Database/MySQL/索引/索引下推.md
❌ Infrastructure/Database/MySQL/锁（Lock）/锁粒度（Lock Granularity）/行级锁（Row Lock）/临键锁（Next-Key Lock）/
```

## 3 反模式目录

### 反模式 1：知识点泛滥

**问题**："知识点"是万能垃圾桶，什么都能往里扔，导致内容无组织。

**实际路径**：

```
Language/Golang/知识点/
Language/Python/知识点/
Language/Shell/知识点/
Language/PHP/知识点/
Language/Node/知识点/
Algorithm/知识点/
Infrastructure/Database/MySQL/知识点/
Infrastructure/Database/PostgresSQL/知识点/
Infrastructure/Database/知识点/
Infrastructure/Docker/知识点/
Infrastructure/Redis/知识点/
Infrastructure/Nginx/知识点/
Infrastructure/Linux/General/知识点/
Infrastructure/Linux/CentOS/知识点/
Infrastructure/Linux/知识点/
Infrastructure/Queue/Kafka/知识点/
Infrastructure/Queue/知识点/
Infrastructure/Queue/RocketMQ/知识点/
Infrastructure/Kubernetes/知识点/
Theory/网络（Network）/知识点/
Theory/人工智能/知识点/
SoftWare/CURL/知识点/
SoftWare/Git/知识点/
Microservices/分布式中间件/分布式API网关（API Gateway）/模块/降级熔断限流/知识点/
Thooh/RPM打包/知识点/
```

共 26 个"知识点"目录。

**修复方案**：将"知识点"目录下的内容按主题重新归类到 L3/L4 层级，删除"知识点"目录。

```
❌ Language/Golang/知识点/进程 & 线程 & 协程.md
✅ Language/Golang/并发/进程与线程与协程.md

❌ Infrastructure/Database/MySQL/知识点/事务.md
✅ Infrastructure/Database/MySQL/事务.md

❌ Infrastructure/Docker/知识点/Dockerfile/ENV & ARG.md
✅ Infrastructure/Docker/Dockerfile/ENV与ARG.md
```

### 反模式 2：深层单文件目录

**问题**：为 1 个文件创建 5-9 层目录，导航成本极高。

**实际路径**：

```
# 深度 10，1 个文件
Other/项目管理/Project/Medlinker/文档/数据库开发规范/ElasticSearch开发规范/ElasticSearch学习指南/学习指南要点/

# 深度 8，1 个文件
Microservices/分布式中间件/权限系统（Permission System）/权限模型/RBAC模型/RBAC模型/数据库设计/

# 深度 8，1 个文件
Language/Golang/文档（document）/Go 语言圣经/第5章 函数/函数(function)/内置函数/

# 深度 8，1 个文件
Infrastructure/Database/MySQL/锁（Lock）/锁粒度（Lock Granularity）/行级锁（Row Lock）/临键锁（Next-Key Lock）/
```

**修复方案**：扁平化到 L4 以内，用文件名编码层级信息。

```
❌ Other/项目管理/Project/Medlinker/文档/数据库开发规范/ElasticSearch开发规范/ElasticSearch学习指南/学习指南要点/
✅ Other/项目管理/Medlinker/ElasticSearch学习指南要点.md

❌ Microservices/分布式中间件/权限系统（Permission System）/权限模型/RBAC模型/RBAC模型/数据库设计/
✅ Microservices/分布式中间件/权限系统/RBAC数据库设计.md

❌ Infrastructure/Database/MySQL/锁（Lock）/锁粒度（Lock Granularity）/行级锁（Row Lock）/临键锁（Next-Key Lock）/
✅ Infrastructure/Database/MySQL/锁/临键锁.md
```

### 反模式 3：混合组织原则

**问题**：同一主题下同时使用"知识点""梳理""文档""第三方包"等按内容类型分类的目录，导致相关内容分散。

**实际路径**：

```
Language/Golang/
├── 知识点/          ← 按内容类型
├── 梳理/            ← 按内容类型
├── 文档（document）/ ← 按内容类型
└── 第三方包/         ← 按内容类型
```

**修复方案**：按主题重组，内容类型通过标签或文件名区分。

```
Language/Golang/
├── 并发/              ← 主题
│   ├── 协程.md
│   ├── sync-Map.md
│   ├── 互斥锁与自旋锁与原子操作.md
│   └── 进程与线程与协程.md
├── 语法/              ← 主题
│   ├── 反射.md
│   ├── 接口.md
│   ├── 切片.md
│   └── 通道.md
├── 第三方库/          ← 主题
│   ├── gRPC.md
│   ├── GORM.md
│   └── Kafka客户端.md
└── 读书笔记/          ← 主题（文档翻译类）
    └── Go语言圣经-函数.md
```

### 反模式 4：冗余命名

**问题**：目录名重复父目录含义，或父子目录同名。

**实际路径**：

```
Algorithm/设计模式/设计模式/          ← "设计模式"出现两次
Algorithm/设计模式/设计模式/访问者模式（Vistor）/
Microservices/分布式中间件/权限系统（Permission System）/权限模型/RBAC模型/RBAC模型/
Infrastructure/Database/MySQL/高性能MySQL/复制/复制（Replication）/
```

**修复方案**：消除冗余层级，子目录名不重复父目录信息。

```
❌ Algorithm/设计模式/设计模式/访问者模式（Vistor）/
✅ Algorithm/设计模式/访问者模式.md

❌ Microservices/分布式中间件/权限系统/权限模型/RBAC模型/RBAC模型/
✅ Microservices/分布式中间件/权限系统/RBAC模型.md

❌ Infrastructure/Database/MySQL/高性能MySQL/复制/复制（Replication）/
✅ Infrastructure/Database/MySQL/复制/Replication配置.md
```

### 反模式 5：内容类型目录

**问题**："梳理""文档""官方文档""问题收集""错误收集"等目录名描述的是内容形式，而非知识主题。

**实际路径**：

```
Language/Golang/梳理/                    ← "梳理"是什么主题？
Language/Golang/文档（document）/          ← 文档翻译混在语言目录
Infrastructure/Database/MySQL/问题收集/   ← 问题属于哪个主题？
Infrastructure/Database/PostgresSQL/错误/  ← 错误属于哪个主题？
Infrastructure/Database/PostgresSQL/错误收集/
Infrastructure/Database/PostgresSQL/Postgres/问题点/
Infrastructure/Nginx/问题/
```

**修复方案**：按主题归类，用标签或文件名前缀标记内容类型。

```
❌ Language/Golang/梳理/语法/反射（reflect）/反射概述.md
✅ Language/Golang/语法/反射.md

❌ Infrastructure/Database/MySQL/问题收集/
✅ Infrastructure/Database/MySQL/常见问题.md（或用标签 #问题 标记）

❌ Infrastructure/Nginx/问题/Nginx配置 转发URL中包含特殊字符.md
✅ Infrastructure/Nginx/配置/URL特殊字符转发.md
```

### 反模式 6：个人/主题边界混淆

**问题**：Thooh/ 目录下混合了个人笔记和技术主题。

**实际路径**：

```
Thooh/RPM打包/知识点/    ← RPM打包是技术主题，不是个人笔记
Thooh/Template/          ← 模板是通用资源
```

**修复方案**：技术主题归入对应领域目录，Thooh/ 只保留真正的个人内容。

```
❌ Thooh/RPM打包/知识点/
✅ Infrastructure/Linux/RPM打包.md

❌ Thooh/Template/
✅ 移至 vault 根目录或 SoftWare/ 下的模板目录
```

### 反模式 7：日期目录与主题混合

**问题**：日期目录和主题目录混在同一层级，破坏主题导航。

**实际路径**：

```
Other/项目管理/Project/Note/study/2020-08-18/    ← 日期作为目录
Other/项目管理/Project/Note/Company/medlinker/daily record/  ← 日常记录
```

**修复方案**：日期用文件名或属性记录，不作为目录名。

```
❌ Other/项目管理/Project/Note/study/2020-08-18/
✅ Other/学习笔记/2020-08-18-行测.md（用 frontmatter date 属性）
```

### 反模式 8：特殊字符和括号翻译

**问题**：目录名包含中文括号、英文括号、&符号、"todo"标记等，影响搜索和链接。

**实际路径**：

```
Microservices/分布式中间件/分布式锁（Distributed Lock）/
Microservices/分布式中间件/分布式API网关（API Gateway）/
Microservices/分布式中间件/分布式事务（Distributed Transaction）/TCC（Try-Confirm-Cancle）/
Infrastructure/Database/MySQL/多版本并发控制（Multi-Version Concurrency Control，MVCC）/
Language/Golang/知识点/进程 & 线程 & 协程/
Language/Golang/知识点/互斥锁 & 自旋锁 & 原子操作/
Infrastructure/Linux/General/VI & VIM/
Microservices/分布式中间件/分布式一致性算法 todo/
Infrastructure/Database/PostgresSQL/显示锁定 todo/
```

**修复方案**：去掉括号翻译，用连字符替代 &，去掉 todo 标记。

```
❌ Microservices/分布式中间件/分布式锁（Distributed Lock）/
✅ Microservices/分布式中间件/分布式锁/

❌ Language/Golang/知识点/进程 & 线程 & 协程/
✅ Language/Golang/并发/进程与线程与协程.md

❌ Microservices/分布式中间件/分布式一致性算法 todo/
✅ Microservices/分布式中间件/分布式一致性算法/
```

### 反模式 9：拼写错误

**问题**：目录名包含拼写错误，影响搜索和链接。

**实际路径与修复**：

| 错误路径 | 正确拼写 |
|----------|----------|
| `Infrastructure/Linux/Apline/` | `Infrastructure/Linux/Alpine/` |
| `Theory/Liunx IO模型/` | `Theory/Linux-IO模型/` |
| `Algorithm/设计模式/设计模式/访问者模式（Vistor）/` | `Algorithm/设计模式/访问者模式/` |
| `Microservices/分布式中间件/分布式事务/.../TCC（Try-Confirm-Cancle）/` | `TCC（Try-Confirm-Cancel）/` |

### 反模式 10：WebClipper 内容未整理

**问题**：WebClipper 保存的网页标题直接作为目录名，包含特殊字符和冗长文本。

**实际路径**：

```
Microservices/DevOps/Operation/Docker-Compose/问题/多个docker-compose文件共享一个网络_海里的顽石的博客-CSDN博客/
```

**修复方案**：WebClipper 内容整理后归入主题目录，用简洁文件名。

```
❌ Microservices/DevOps/Operation/Docker-Compose/问题/多个docker-compose文件共享一个网络_海里的顽石的博客-CSDN博客/
✅ Infrastructure/Docker/Docker-Compose网络共享.md
```

## 4 命名规范

### 4.1 语言

- 目录名和文件名以**中文**为主
- 英文专有名词保持原文：`Kubernetes`、`Redis`、`gRPC`
- 不在中文后加括号英文翻译：`分布式锁` 而非 `分布式锁（Distributed Lock）`

### 4.2 禁止的命名模式

| 禁止模式 | 原因 | 替代方案 |
|----------|------|----------|
| `知识点` | 万能垃圾桶，无主题意义 | 按实际主题归类 |
| `梳理` | 内容类型，非主题 | 按实际主题归类 |
| `文档` / `文档（document）` | 内容类型，非主题 | 按实际主题归类 |
| `官方文档` | 内容类型，非主题 | 按实际主题归类 |
| `问题收集` / `错误收集` | 内容类型，非主题 | 归入主题，用标签标记 |
| `其他` / `杂项` | 无分类意义 | 按实际主题归类 |
| `todo` / `TODO` | 状态标记不属于目录名 | 用 Obsidian 任务标记或标签 |
| `备注` | 内容类型，非主题 | 内容合并到主笔记 |
| `（中文翻译）` | 括号翻译冗余 | 用中文或英文，不混用 |

### 4.3 特殊字符

| 禁止字符 | 原因 | 替代方案 |
|----------|------|----------|
| `（` `）` | 中文括号在文件系统中易出问题 | 去掉括号翻译 |
| `(` `)` | 英文括号增加路径复杂度 | 去掉括号翻译 |
| `【` `】` | 特殊符号，搜索不友好 | 不使用 |
| `&` | 命令行和 URL 中有特殊含义 | 用 `与` 或 `-` 替代 |
| 空格 | 路径中需转义 | 用 `-` 或中文无空格 |

### 4.4 大小写

- 英文专有名词保持官方大小写：`Kubernetes`、`Redis`、`gRPC`、`MySQL`
- 中文目录名不涉及大小写
- 文件名中英文部分保持官方大小写

### 4.5 拼写纠错清单

| 错误 | 正确 | 路径 |
|------|------|------|
| `Apline` | `Alpine` | `Infrastructure/Linux/Apline/` |
| `Liunx` | `Linux` | `Theory/Liunx IO模型/` |
| `Vistor` | `Visitor` | `Algorithm/设计模式/设计模式/访问者模式（Vistor）/` |
| `Cancle` | `Cancel` | `Microservices/分布式中间件/分布式事务/.../TCC（Try-Confirm-Cancle）/` |
| `PostgresSQL` | `PostgreSQL` | `Infrastructure/Database/PostgresSQL/` |

## 5 扁平化规则

### 5.1 何时扁平化

满足以下任一条件时，必须扁平化：

1. **深度超过 4 层**：从 vault 根算起超过 L4
2. **单文件目录**：目录下只有 1 个文件且无子目录
3. **冗余层级**：子目录名重复父目录信息
4. **内容类型目录**：目录名是"知识点""梳理""文档"等

### 5.2 如何扁平化

**步骤一**：确定内容所属的 L1-L2 主题路径。

**步骤二**：确定 L3 子主题。如果内容属于已有子主题，归入；否则创建新子主题。

**步骤三**：用文件名编码原路径中的细节信息。

**步骤四**：用 Obsidian 标签或 frontmatter 记录元信息（内容类型、来源等）。

### 5.3 扁平化示例

#### 示例 1：MySQL 锁

```
❌ Before:
Infrastructure/Database/MySQL/锁（Lock）/
├── 锁粒度（Lock Granularity）/
│   ├── 表级锁（Table Lock）/
│   │   ├── 意向锁（Intention Lock）/
│   │   └── 元数据锁（Meta Data Lock，MDL）/
│   ├── 行级锁（Row Lock）/
│   │   └── 临键锁（Next-Key Lock）/
│   ├── 页级锁（Page Lock）/
│   └── 其他锁/
└── 索引下推（index condition pushdown，ICP）/

✅ After:
Infrastructure/Database/MySQL/锁/
├── 意向锁.md
├── 元数据锁.md
├── 临键锁.md
├── 页级锁.md
├── 其他锁类型.md
└── 索引下推.md
```

#### 示例 2：设计模式

```
❌ Before:
Algorithm/设计模式/
├── 编程规范/
└── 设计模式/
    ├── 备忘录模式（Memento）/
    ├── 策略模式（Strategy）/
    ├── 抽象工厂模式（Abstract Factory）/
    ├── 代理模式（Proxy）/
    ├── 单例模式（Singleton）/
    ├── 访问者模式（Vistor）/    ← 拼写错误
    └── ... (20+ 个模式，每个一个目录)

✅ After:
Algorithm/设计模式/
├── 编程规范.md
├── 备忘录模式.md
├── 策略模式.md
├── 抽象工厂模式.md
├── 代理模式.md
├── 单例模式.md
├── 访问者模式.md
└── ...
```

#### 示例 3：Golang 语言

```
❌ Before:
Language/Golang/
├── 知识点/
│   ├── 进程 & 线程 & 协程/
│   │   ├── 进程/
│   │   └── 协程/
│   ├── 互斥锁 & 自旋锁 & 原子操作/
│   ├── Base64编码类型/
│   ├── database sql/
│   └── ...
├── 梳理/
│   ├── 安装/
│   ├── 函数/
│   │   └── sync Map/
│   ├── 语法/
│   │   ├── 反射（reflect）/
│   │   │   ├── 反射概述/
│   │   │   ├── 反射要点1/
│   │   │   └── 反射要点2/
│   │   ├── 接口（interface）/
│   │   └── ...
│   └── ...
├── 文档（document）/
│   ├── Go 语言圣经/
│   │   ├── 第5章 函数/
│   │   │   └── 函数(function)/
│   │   │       └── 内置函数/
│   │   └── ...
│   └── 官方文档/
└── 第三方包/
    ├── 框架（Frame）/gRPC/
    ├── 数据库（ORM）/gorm io gorm/
    └── ...

✅ After:
Language/Golang/
├── 语法/
│   ├── 反射.md
│   ├── 接口.md
│   ├── 切片.md
│   ├── 通道.md
│   ├── 结构体.md
│   ├── 指针.md
│   └── 变量.md
├── 并发/
│   ├── 协程.md
│   ├── sync-Map.md
│   ├── 互斥锁与自旋锁与原子操作.md
│   └── 进程与线程与协程.md
├── 函数/
│   ├── 内置函数.md
│   └── 值传递与指针传递.md
├── 第三方库/
│   ├── gRPC.md
│   ├── GORM.md
│   ├── Kafka客户端.md
│   └── NSQ.md
├── 工具/
│   ├── golangci-lint.md
│   └── 插件管理.md
├── 编码/
│   ├── Base64编码.md
│   ├── JSON高性能字段名.md
│   └── 数据转换.md
├── 网络/
│   ├── HTTP超时机制.md
│   └── 信号处理.md
├── 测试/
│   └── 单元测试成神之路.md
├── 安装与环境/
│   └── ...
└── 读书笔记/
    └── Go语言圣经/
        ├── 函数.md
        ├── 接口.md
        └── ...
```

#### 示例 4：分布式中间件

```
❌ Before:
Microservices/分布式中间件/
├── 分布式锁（Distributed Lock）/
├── 负载均衡（Load Balance）/
├── 权限系统（Permission System）/
│   └── 权限模型/
│       └── RBAC模型/
│           └── RBAC模型/
│               └── 数据库设计/
├── 分布式API网关（API Gateway）/
│   ├── 模块/
│   │   ├── 负载均衡（Load Balance，LB)/
│   │   └── 降级熔断限流/
│   │       ├── 服务熔断/
│   │       ├── 服务限流/
│   │       │   ├── 限流器/
│   │       │   └── 限流阈值/
│   │       └── 知识点/
│   └── 架构/
│       └── Openresty/
├── 分布式事务（Distributed Transaction）/
│   ├── TCC（Try-Confirm-Cancle）/
│   │   └── 两阶段提交（Two-phase Commit， 2PC）/
│   │       └── XA规范/
│   └── ...
└── 分布式一致性算法 todo/

✅ After:
Microservices/分布式中间件/
├── 分布式锁/
├── 负载均衡/
├── 权限系统/
│   └── RBAC数据库设计.md
├── API网关/
│   ├── 降级熔断限流.md
│   ├── 限流器.md
│   ├── 限流阈值.md
│   ├── 服务熔断.md
│   └── OpenResty架构.md
├── 分布式事务/
│   ├── TCC.md
│   ├── 两阶段提交.md
│   ├── XA规范.md
│   ├── 可靠消息最终一致性.md
│   └── CAP理论.md
├── 分布式一致性算法/
│   ├── Raft算法.md
│   └── CRDT.md
├── 服务发现/
│   └── Etcd/
├── 分布式缓存/
│   └── Redis/
├── 消息队列/
│   ├── Nats.md
│   └── ...
├── 配置中心/
└── 定时任务与延迟消息/
```

## 6 迁移指南

### 6.1 迁移优先级

按影响程度从高到低排序：

| 优先级 | 任务 | 影响范围 | 预估文件数 |
|--------|------|----------|-----------|
| P0 | 修复拼写错误目录 | 4 个目录 | ~10 |
| P1 | 消除"知识点"目录 | 26 个目录 | ~100+ |
| P2 | 消除"梳理""文档"等内容类型目录 | ~5 个目录 | ~50+ |
| P3 | 扁平化超过 4 层的深层目录 | ~30 个路径 | ~50+ |
| P4 | 去除括号翻译和特殊字符 | ~50+ 个目录 | ~100+ |
| P5 | 合并冗余命名目录 | ~10 个路径 | ~30+ |
| P6 | 重组 Golang 混合组织 | 1 个主题 | ~50+ |
| P7 | 整理 Thooh/ 中的技术内容 | 1 个目录 | ~5 |
| P8 | 清理 WebClipper 长标题 | ~5 个路径 | ~5 |

### 6.2 迁移步骤

每个迁移任务按以下步骤执行：

#### 步骤 1：创建目标目录

按命名规范创建新的目录结构。

#### 步骤 2：移动文件

将文件从旧路径移动到新路径。使用 Obsidian 的移动功能以自动更新内部链接。

#### 步骤 3：更新链接

检查并修复所有指向旧路径的 `[[wikilinks]]`。

#### 步骤 4：验证

- 确认所有文件在新路径下可访问
- 确认无断链
- 确认目录深度不超过 4 层

#### 步骤 5：删除空目录

清理迁移后留下的空目录。

### 6.3 迁移检查清单

- [ ] P0：拼写错误修复
  - [ ] `Apline` → `Alpine`
  - [ ] `Liunx` → `Linux`
  - [ ] `Vistor` → `Visitor`
  - [ ] `Cancle` → `Cancel`
  - [ ] `PostgresSQL` → `PostgreSQL`
- [ ] P1：知识点目录消除
  - [ ] `Language/Golang/知识点/` → 按主题归类
  - [ ] `Language/Python/知识点/` → 按主题归类
  - [ ] `Language/Shell/知识点/` → 按主题归类
  - [ ] `Language/PHP/知识点/` → 按主题归类
  - [ ] `Language/Node/知识点/` → 按主题归类
  - [ ] `Algorithm/知识点/` → 按主题归类
  - [ ] `Infrastructure/Database/MySQL/知识点/` → 按主题归类
  - [ ] `Infrastructure/Database/PostgresSQL/知识点/` → 按主题归类
  - [ ] `Infrastructure/Database/知识点/` → 按主题归类
  - [ ] `Infrastructure/Docker/知识点/` → 按主题归类
  - [ ] `Infrastructure/Redis/知识点/` → 按主题归类
  - [ ] `Infrastructure/Nginx/知识点/` → 按主题归类
  - [ ] `Infrastructure/Linux/General/知识点/` → 按主题归类
  - [ ] `Infrastructure/Linux/CentOS/知识点/` → 按主题归类
  - [ ] `Infrastructure/Linux/知识点/` → 按主题归类
  - [ ] `Infrastructure/Queue/Kafka/知识点/` → 按主题归类
  - [ ] `Infrastructure/Queue/知识点/` → 按主题归类
  - [ ] `Infrastructure/Queue/RocketMQ/知识点/` → 按主题归类
  - [ ] `Infrastructure/Kubernetes/知识点/` → 按主题归类
  - [ ] `Theory/网络（Network）/知识点/` → 按主题归类
  - [ ] `Theory/人工智能/知识点/` → 按主题归类
  - [ ] `SoftWare/CURL/知识点/` → 按主题归类
  - [ ] `SoftWare/Git/知识点/` → 按主题归类
  - [ ] `Microservices/分布式中间件/.../知识点/` → 按主题归类
  - [ ] `Thooh/RPM打包/知识点/` → 按主题归类
- [ ] P2：内容类型目录消除
  - [ ] `Language/Golang/梳理/` → 按主题归类
  - [ ] `Language/Golang/文档（document）/` → 按主题归类
  - [ ] `Infrastructure/Database/MySQL/问题收集/` → 按主题归类
  - [ ] `Infrastructure/Database/PostgresSQL/错误/` → 按主题归类
  - [ ] `Infrastructure/Database/PostgresSQL/错误收集/` → 按主题归类
- [ ] P3：深层目录扁平化
  - [ ] MySQL 锁相关路径（深度 8）
  - [ ] 权限系统 RBAC 路径（深度 8）
  - [ ] API 网关限流路径（深度 8）
  - [ ] 高性能 MySQL 翻译路径（深度 8-11）
  - [ ] 项目管理深层路径（深度 9-10）
- [ ] P4：括号翻译和特殊字符清理
  - [ ] 去除所有目录名中的 `（英文翻译）` 模式
  - [ ] 替换 `&` 为 `与` 或 `-`
  - [ ] 去除目录名中的 `todo` 标记
- [ ] P5：冗余命名合并
  - [ ] `Algorithm/设计模式/设计模式/` → `Algorithm/设计模式/`
  - [ ] `权限系统/权限模型/RBAC模型/RBAC模型/` → `权限系统/RBAC模型.md`
  - [ ] `高性能MySQL/复制/复制（Replication）/` → `复制/Replication配置.md`
- [ ] P6：Golang 目录重组
  - [ ] 合并 `知识点/`、`梳理/`、`文档/`、`第三方包/` 到主题目录
- [ ] P7：Thooh 技术内容迁移
  - [ ] `Thooh/RPM打包/` → `Infrastructure/Linux/RPM打包.md`
- [ ] P8：WebClipper 长标题清理
  - [ ] 重命名 WebClipper 保存的过长目录名

### 6.4 迁移注意事项

1. **使用 Obsidian 移动功能**：在 Obsidian 中拖拽文件移动，自动更新内部链接。
2. **分批迁移**：每次只迁移一个主题（如 MySQL 或 Golang），迁移完验证后再继续。
3. **保留 git 历史**：使用 `git mv` 命令移动文件，保留变更历史。
4. **备份优先**：迁移前确保 vault 有完整备份。
5. **链接检查**：迁移后使用 Obsidian 的链接检查功能确认无断链。
6. **渐进式迁移**：不要求一次完成所有迁移，按优先级逐步推进。