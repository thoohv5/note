---
title: 存储过程faker数据
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## 存储过程faker数据

### 表结构

```sql
CREATE TABLE `users` (
    `id` BIGINT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键ID',
    `identity` VARCHAR(64) NOT NULL COMMENT '用户唯一标识',
    `name` VARCHAR(100) NOT NULL COMMENT '用户名',
    `head_portrait` VARCHAR(200) DEFAULT NULL COMMENT '头像URL',
    `type` TINYINT UNSIGNED NOT NULL DEFAULT 0 COMMENT '用户类型（0~10）',
    `is_disable` TINYINT(1) NOT NULL DEFAULT 0 COMMENT '是否禁用：0正常，1禁用',
    `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `updated_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_identity` (`identity`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

```

### 随机字符串函数

```sql
delimiter //
DROP FUNCTION IF EXISTS `rand_string` //
CREATE DEFINER = CURRENT_USER FUNCTION `rand_string` (n INT) RETURNS VARCHAR(255) CHARSET 'utf8mb4'
DETERMINISTIC
BEGIN 
    DECLARE char_str varchar(100) DEFAULT 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
    DECLARE return_str varchar(255) DEFAULT '';
    DECLARE i INT DEFAULT 0;
    WHILE i < n DO
        SET return_str = concat(return_str, substring(char_str, FLOOR(1 + RAND()*62), 1));
        SET i = i+1;
    END WHILE;
    RETURN return_str;
END //
delimiter;
```

### 构造数据函数

```sql
delimiter //
DROP PROCEDURE IF EXISTS batch_insert //
CREATE PROCEDURE batch_insert (in max_num int)
BEGIN
	DECLARE i INT DEFAULT 0;
	SET autocommit = 0;
	WHILE i <= max_num DO
			INSERT INTO `users`(`identity`, `name`, `head_portrait`, `type`, `is_disable`) 
			VALUES (rand_string(64), rand_string(100), rand_string(200), i%11, 0);
		SET i = i + 1;
	END WHILE;
	COMMIT;
END // 
delimiter;
```