---
title: 日志轮转（Logrotate）
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: guide
status: complete
---

## 日志轮转（Logrotate）

## 概述

**`logrotate`** 是 Linux/Unix 系统中专门用于管理日志轮转的工具。

### **特点**

- 支持定时自动轮转
- 可针对不同日志文件制定不同策略
- 支持压缩、删除、邮件发送等操作
- 可结合自定义脚本执行复杂操作

大部分 Linux 发行版（如 CentOS、Ubuntu、Rocky Linux）都预装了 logrotate。系统日志、Nginx、MySQL、Tomcat 等日志轮转大多基于它实现。

## 安装

```go
## 版本
logrotate --version

## 安装
yum install -y logrotate
```

## 配置

### 全局配置

`/etc/logrotate.conf`文件设置服务器的统一默认规则，适用于大多数普通日志

### **推荐的全局配置**

```yaml
## 全局轮转周期
weekly# 每周轮转一次
rotate 4# 保留最近4次的归档
create# 每次轮转后新建日志
compress# 归档文件 gzip 压缩
delaycompress# 延迟一轮再压缩
missingok# 日志丢失不报错
notifempty# 日志为空不轮转
dateext# 使用日期作为后缀，如 .log-20250426.gz

## 包含单独服务配置
include /etc/logrotate.d
```

### 子配置

子配置文件`/etc/logrotate.d/*`针对每个应用（如 nginx、mysql、docker）单独定制。

- **Nginx配置示例：**`/etc/logrotate.d/nginx`：
    
    ```yaml
    /var/log/nginx/*.log {
        daily
        missingok
        rotate 14
        compress
        delaycompress
        notifempty
        create 0640 nginx adm
        sharedscripts
        postrotate
            [ -s /run/nginx.pid ] && kill -USR1 $(cat /run/nginx.pid)
        endscript
    }
    ```
    
- **MySQL配置示例：**
    
    ```yaml
    /var/log/mysql/*.log {
        weekly
        rotate 8
        missingok
        compress
        notifempty
        create 640 mysql mysql
        sharedscripts
        postrotate
            /usr/bin/mysqladmin --defaults-file=/etc/mysql/debian.cnf flush-logs
        endscript
    }
    
    ```
    

### **配置参数详解**

| **指令** | **说明** |
| --- | --- |
| `daily`/`weekly`/`monthly` | 每天/每周/每月轮转 |
| `rotate N` | 保留 N 个轮转文件 |
| `compress` | 使用 gzip 压缩 |
| `delaycompress` | 延迟到下次轮转才压缩 |
| `missingok` | 如果日志不存在，不报错 |
| `notifempty` | 如果日志为空，不轮转 |
| `create MODE OWNER GROUP` | 创建新日志文件并设置权限 |
| `postrotate endscript` | 轮转完成后执行脚本，如重载服务 |

## **工作原理**

`logrotate` 通过**定时任务（cron）**每天自动执行，流程如下：

1. **定时触发**：由 `/etc/cron.daily/logrotate` 脚本调度
2. **加载配置**：读取 `/etc/logrotate.conf` 和 `/etc/logrotate.d/`*
3. **检查日志文件**：
    1. 是否满足轮转条件（时间、大小）
    2. 文件是否存在、是否为空
4. **执行轮转动作**：
    1. 重命名旧日志（加时间后缀）
    2. 压缩归档
    3. 创建新的日志文件
    4. 删除超期的旧日志
5. **执行钩子脚本**（postrotate）
    
    如果一切正常，轮转过程对应用程序是**透明**的。
    

## **手动测试与故障排查**

有时候需要手动测试 `logrotate` 是否配置正确，方法如下：

**手动测试单个配置文件**

```bash
sudo logrotate -f /etc/logrotate.d/nginx
```

**带详细日志模式**

```bash
sudo logrotate -vf /etc/logrotate.d/nginx

```

- `f` 强制轮转
- `v` 显示详细执行过程

**常见错误排查：**

- 配置路径错误
- 权限不足，无法新建日志文件
- postrotate脚本出错，导致服务无法重新打开日志

查看系统日志 `/var/log/messages` 可以帮助进一步排查。