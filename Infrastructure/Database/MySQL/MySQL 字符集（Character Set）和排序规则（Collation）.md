---
title: MySQL 字符集（Character Set）和排序规则（Collation）
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: reference
status: complete
---

## MySQL 字符集（Character Set）和排序规则（Collation）

### 一、基本概念

### 字符集（Character Set）

**定义：**字符集定义了 **一组可以存储的字符**，比如英文字母、中文、符号、表情符号等。

例如：

| 字符集 | 描述 |
| --- | --- |
| `latin1` | 仅支持西欧字符 |
| `utf8` | 支持大部分语言字符（但最多3字节） |
| `utf8mb4` | 完整 UTF-8 实现（最多4字节，可存 emoji） |
| `gbk` | 简体中文字符集 |
| `ascii` | 仅支持英文字母和数字 |

---

### 排序规则（Collation）

**定义：**排序规则定义了**字符比较和排序的方式**。

例如：

| 排序规则 | 说明 |
| --- | --- |
| `utf8_general_ci` | 不区分大小写（ci = case-insensitive） |
| `utf8_bin` | 区分大小写（bin = binary 比较） |
| `utf8mb4_unicode_ci` | **基于 Unicode 排序，不区分大小写，Unicode 官方标准算法** |
| `utf8mb4_general_ci` | 基于 Unicode 排序，不区分大小写，MySQL 自定义的简化算法 |
| `utf8mb4_bin` | 基于 Unicode 排序，区分大小写（bin = binary 比较） |
| `utf8mb4_0900_ai_ci` | **MySQL 8.0 推荐的新版 Unicode 排序（ai = accent-insensitive）** |

---

- **ci & ai**
    
    <aside>
    💡
    
    ### `ai` = accent-insensitive（**不区分重音符号**）
    
    字母重音（é、è、ê）等视为相同字母。
    
    如果你希望区分重音，可以用 `utf8mb4_0900_as_ci`（as = accent-sensitive）。
    
    ---
    
    ### 4️⃣ `ci` = case-insensitive（**不区分大小写**）
    
    `'A'` 与 `'a'` 在比较和排序时视为相同。
    
    </aside>
    

### 两者关系

- 一个 **字符集** 可以有 **多个排序规则**；
- 每个 **排序规则** 都依赖某个 **字符集**；
- 例如：
    
    ```
    字符集: utf8mb4
        ├── utf8mb4_general_ci
        ├── utf8mb4_unicode_ci
        ├── utf8mb4_bin
        └── utf8mb4_0900_ai_ci (MySQL 8.0 新版)
    ```
    

---

### 层级设置

MySQL 的字符集和排序规则可以在多个层级定义：

| 层级 | 示例命令 | 作用范围 |
| --- | --- | --- |
| 服务器级 | `SHOW VARIABLES LIKE 'character_set_%';` | 整个 MySQL 实例 |
| 数据库级 | `CREATE DATABASE db CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;` | 当前数据库 |
| 表级 | `CREATE TABLE t (...) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;` | 当前表 |
| 列级 | `CREATE TABLE t (name VARCHAR(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin);` | 当前列 |
| 连接级 | `SET NAMES utf8mb4;
等价上语句
SET character_set_client = utf8mb4;  # 客户端发送编码
SET character_set_connection = utf8mb4; # MySQL解析编码
SET character_set_results = utf8mb4; # MySQL返回编码`   | 当前客户端连接 |

⚠️ 优先级顺序：

```
列级 > 表级 > 数据库级 > 服务器级

连接级（Connection-level）并不影响数据库存储的字符集，
它影响的是 客户端与 MySQL 服务器之间传输时的编码转换。
```

---

### 常用命令

### 查看全局字符集设置

```sql
## 字符集
SHOW VARIABLES LIKE 'character_set_%';
## 排序规则
SHOW VARIABLES LIKE 'collation_%';
```

---

### 推荐配置（现代系统）

在 **MySQL 8.0** 或新项目中，建议统一使用：

```sql
CHARACTER SET utf8mb4
COLLATE utf8mb4_0900_ai_ci
```

原因：

- `utf8mb4` 能完整支持所有 Unicode 字符（**包括 emoji 😄**）
- `utf8mb4_0900_ai_ci` 排序性能好且兼容国际化

---

### ⚠️常见问题

| 问题 | 原因 | 解决方案 |
| --- | --- | --- |
| 中文显示为 `???` | 客户端编码与表编码不一致 | 执行 `SET NAMES utf8mb4;` |
| emoji 插入失败 | 使用了 `utf8`（最多3字节） | 改用 `utf8mb4` |
| 排序异常（A < a） | 使用 `utf8_bin` 等区分大小写排序 | 改用 `utf8mb4_general_ci` |
| 联表排序报错 “Illegal mix of collations” | 两列排序规则不同 | 在 SQL 中用 `COLLATE` 明确指定 |

```sql
## When comparing nonbinary strings (CHAR, VARCHAR, TEXT),
## MySQL ignores trailing spaces.
SELECT _utf8mb4'1' COLLATE utf8mb4_unicode_ci = _utf8mb4'1 ' COLLATE utf8mb4_unicode_ci;

SELECT 'a' = 'a ';     -- 1
SELECT BINARY 'a' = BINARY 'a ';  -- 0
```