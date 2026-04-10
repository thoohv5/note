# 读锁（Read Lock）

```sql
-- 加读锁，其他会话只能读，不能写
LOCK TABLES orders READ;

-- 执行批量更新操作

UNLOCK TABLES;
```