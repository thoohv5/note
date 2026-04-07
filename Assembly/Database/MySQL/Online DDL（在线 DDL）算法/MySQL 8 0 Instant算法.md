# MySQL 8.0 Instant算法

```sql
ALTER TABLE ... ALGORITHM=INSTANT
```

### 基础

MySQL 在执行 `ALTER TABLE` 时，可以指定不同的算法：

- **COPY**
    
    复制整个表，耗时长，需要更多磁盘空间，期间会阻塞。
    
- **INPLACE【5.7 默认】**
    
    就地修改表，通常比 COPY 快，很多变更支持在线进行，但仍可能涉及数据重写。
    
- **INSTANT [8.0.12 默认]**
    
    不复制表，也不重写数据，仅修改元数据，几乎是瞬时完成。
    

### 支持的操作

`ALGORITHM=INSTANT` 支持的 DDL 操作比较有限，目前主要包括：

1. **添加列（ADD COLUMN）**
    - 只能在 **表的最后** 添加一列。
    - 新增列必须有 **默认值**（或允许 NULL）。
    - 不能在中间插入列（那样需要重写表）。
2. **重命名列（RENAME COLUMN）**
3. **更改列的默认值（ALTER COLUMN ... SET DEFAULT）**
4. **重命名索引（RENAME INDEX）**

### 示例

```sql
-- 添加新列，瞬间完成
ALTER TABLE users ADD COLUMN age INT DEFAULT 0, ALGORITHM=INSTANT;

-- 修改列默认值
ALTER TABLE users ALTER COLUMN age SET DEFAULT 18, ALGORITHM=INSTANT;

-- 重命名列
ALTER TABLE users RENAME COLUMN age TO user_age, ALGORITHM=INSTANT;

```