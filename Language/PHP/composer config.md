---
title: composer config
date: 2026-04-07
tags:
  - 编程语言
  - PHP
type: note
status: complete
source: https://getcomposer.org/doc/
---

## composer config

### 常用配置

```bash
# 全局配置国内镜像源
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

# 取消镜像源
composer config -g --unset repos.packagist

# 查看当前配置
composer config -g --list

# 设置项目级别配置（不加 -g）
composer config repo.packagist composer https://mirrors.aliyun.com/composer/
```

### 国内镜像源

| 镜像 | URL |
|------|-----|
| Aliyun | `https://mirrors.aliyun.com/composer/` |
| Tencent | `https://mirrors.cloud.tencent.com/composer/` |
| 华为 | `https://mirrors.huaweicloud.com/repository/php/` |

### 说明

- `-g` 参数表示全局配置，存储在 `~/.composer/config.json`
- 不加 `-g` 仅影响当前项目 `composer.json`
- `repos.packagist` 可配置多个备用源
- Composer 2.x 已显著减少内存消耗并加速依赖解析

### 相关笔记

- [[composer]]