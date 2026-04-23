---
title: APT（Advanced Package Tool）
date: 2026-04-07
  - 基础设施
  - Linux
type: guide
status: complete
---

## APT（Advanced Package Tool）

`apt-get` 是 Debian 系（包括 Ubuntu）Linux 系统中用于管理软件包的命令行工具，背后使用的是 **APT（Advanced Package Tool）**。

| 命令 | 说明 |
| --- | --- |
| `sudo apt-get update` | 更新软件包索引（不会安装或升级） |
| `sudo apt-get upgrade` | 升级所有已安装软件包 |
| `sudo apt-get install <包名>` | 安装新的软件包 |
| `sudo apt-get remove <包名>` | 卸载软件包（保留配置文件） |
| `sudo apt-get purge <包名>` | 卸载软件包并删除配置文件 |
| `sudo apt-get autoremove` | 删除无用的依赖软件 |
| `sudo apt-get clean` | 清理本地 `.deb` 缓存文件 |
| `sudo apt-get dist-upgrade` | 进行智能升级，处理依赖变更或内核更新 |

`apt` 是 Ubuntu 新版本推荐的简化命令（例如 `apt install`、`apt update`），更适合人类直接使用。

### 安装ping

```c
apt-get update && apt-get install iputils-ping;
```

### 安装telnet

```c
apt-get update && apt-get install telnet;

apk add busybox-extras
```

### 安装ifconfig

```c
apt-get update && apt-get install net-tools;
```

### 安装lsof

```c
apt-get update && apt-get install lsof
```