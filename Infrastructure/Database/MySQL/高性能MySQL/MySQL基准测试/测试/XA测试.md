---
title: XA测试
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: note
status: complete
---

## XA测试

```jsx
XA {START|BEGIN} xid [JOIN|RESUME]    #开始一个分布式事务  
  
XA END xid [SUSPEND [FOR MIGRATE]]  
  
XA PREPARE xid      # 准备提交事务  
  
XA COMMIT xid [ONE PHASE]   # 提交事务  
  
XA ROLLBACK xid   #回滚事务  
  
XA RECOVER [CONVERT XID]   # 查看处于PREPARE状态的事务
```

```jsx
XID标识一个分布式事务，其组成：
xid: gtrid [, bqual [, formatID ]]
gtrid：必须，为字符串，表示全局事务标识符
bqual：可选，为字符串，默认是空串，表示分支限定符
formatID：可选，默认值为1，用于标识由gtrid和bqual值使用的格式
```

```jsx
SHOW VARIABLES LIKE '%XA%'

xa start 'tx1','dn-3306';
update classes set name = '3' where id = 3;
xa end 'tx1', 'dn-3306';
xa prepare 'tx1', 'dn-3306';

xa commit 'tx1', 'dn-3306';

xa rollback 'tx1', 'dn-3306';

xa start 'tx1','dn-3307';
update classes set name = '4' where id = 4;
xa end 'tx1', 'dn-3307';
xa prepare 'tx1', 'dn-3307';

xa commit 'tx1', 'dn-3307';

xa rollback 'tx1', 'dn-3307';

xa recover;
```

[](https://blog.csdn.net/a1282379904/article/details/77894232)