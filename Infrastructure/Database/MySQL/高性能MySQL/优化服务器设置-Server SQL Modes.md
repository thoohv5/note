---
title: Server SQL Modes
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: note
status: complete
---

## Server SQL Modes

在MySQL 5.7的默认SQL模式包括以下模式：ONLY_FULL_GROUP_BY， STRICT_TRANS_TABLES， NO_ZERO_IN_DATE， NO_ZERO_DATE， ERROR_FOR_DIVISION_BY_ZERO， NO_AUTO_CREATE_USER，和 NO_ENGINE_SUBSTITUTION。

```jsx
update apps set owner_code='000' and owner_name='bbb' where owner_code='0' and owner_name='aaa';

CREATE TABLE `apps` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `owner_code` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `owner_name` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

```