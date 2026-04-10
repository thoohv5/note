# MyISAM引擎

### 事务

不支持

### 锁

表级锁

### 索引

FULLTEXT，**前缀索引  // todo**

### 存储结构

1. frm文件：存储表的定义数据
2. MYD文件：存放表具体记录的数据
3. MYI文件：存储索引

### 使用场景

1. 非事务型应用
2. 只读类应用或者表比较小、可忍受修复的操作
3. 空间类应用

### 备注

1. MySQL5.5之前的默认存储引擎
2. 系统表和临时表默认的存储引擎
3. 表损坏修复
    
    ```bash
    check table tablename
    repair table tablename
    ```
    
4. 表数据压缩
    
    ```bash
    myisampack
    ```