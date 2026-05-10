---
title: 基于全局事务标识符（GTID）的复制
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: guide
status: complete
---

## 基于全局事务标识符（GTID）的复制

1. 在主DB服务上建立复制账号
    
    同上
    
2. 配置主数据库服务器【重新启动服务器】
    
    ```bash
    [mysqld]
    server-id=1
    log-bin=master-bin
    log-bin-index=master-bin.index
    
    gtid_mode=on
    enforce-gtid-consistency=on
    ```
    
3. 配置从数据库服务器
    
    ```bash
    [mysqld]
    server-id=2
    log-bin=master-bin
    log-bin-index=master-bin.index
    
    gtid_mode=on
    enforce-gtid-consistency=on
    
    log_save_update=on  #【可选】链路复制
    read_only=on  #【可选】非super用户不能修改
    ```
    
4. 初始化从服务器数据
    
    ```bash
    # 方案一
    mysqldump --master-data=2 single-transaction
    
    # 方案二
    xtrabackup --slave info
    
    ```
    
5. 启动复制链路
    
    ```bash
    change master to master_host='192.168.0.1', //Master 服务器Ip
    master_port=3306,
    master_user='repl',
    master_password='mysql', 
    master_log_file='master-bin.000001',//Master服务器产生的日志
    master_log_pos=0;
    ```
    
6. 验证
    
    ```bash
    SHOW MASTER STATUS;
    ```