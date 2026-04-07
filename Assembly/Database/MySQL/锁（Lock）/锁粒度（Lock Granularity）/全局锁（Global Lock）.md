# 全局锁（Global Lock）

## 概述

**全局锁**是指锁住整个 MySQL **实例的所有库和表**，当加上全局锁后，**其他线程不能进行任何写操作**（插入、更新、删除等），只允许读操作（如查询）。

MySQL 本身没有专门的 "Global Lock" 语法，而是通过如下命令实现类似的全局锁功能：

```sql
FLUSH TABLES WITH READ LOCK;
```

---

## 作用

- **保持数据一致性**
    - 逻辑备份时（如 `mysqldump`），防止数据被写入导致备份不一致。
- **阻止写操作**
    - 在做全量导出或数据迁移前，临时锁定全局写入。
- **辅助主从复制初始化**
    - 主库加全局锁后做备份，从库再恢复数据并同步 binlog。

---

## 示例

```sql
# FTWRL
FLUSH TABLES WITH READ LOCK;

-- 执行批量更新操作

UNLOCK TABLES;
```

---

## 注意事项

官方自带的逻辑备份工具是 `mysqldump`。当 `mysqldump` 使用参数`–single-transaction` 的时候，导数据之前就会启动一个事务，来确保拿到一致性视图。而由于 **MVCC** 的支持，这个过程中数据是可以正常更新的。

```sql
mysqldump --single-transaction -u root -p your_database > backup.sql
```