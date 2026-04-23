---
title: 锁类型（Lock Type）
date: 2026-04-07
  - 基础设施
  - 数据库
type: reference
status: complete
---

## 锁类型（Lock Type）

### 悲观锁（Pessimistic Lock）

**工作原理**：基于"先验冲突"假设，在事务开始时就加锁。

典型实现方式：

```sql
START TRANSACTION;
SELECT * FROM accounts WHERE id=1 FOR UPDATE;  // 显式加锁
// 业务处理...
COMMIT;
```

**特征**：

- 排他性控制：通过SELECT...FOR UPDATE锁定目标记录
- 事务隔离：锁定持续到事务结束
- 典型应用：账户余额修改、库存扣减等高并发写场景

### 乐观锁（Optimistic Lock）

**实现机制**：

```sql
// 读取时获取版本号
$row = query("SELECT balance, version FROM accounts WHERE id=1");

// 更新时校验版本
$result = execute(    
	"UPDATE accounts SET balance=:new_balance, version=version+1
	WHERE id=1 AND version=:old_version",    
	[':new_balance' => $new_balance, ':old_version' => $row['version']]
);
if ($result->rowCount() === 0) {    
	throw new OptimisticLockException("并发修改冲突");
}
```

**优势对比**：

- 无锁读取：提升系统吞吐量
- 冲突检测：通过版本号/时间戳实现轻量级校验
- 适用场景：读多写少、冲突概率低的业务场景