---
title: RSYSLOG
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: complete
---

# RSYSLOG

```

ps aux | grep "rsyslog" | grep -v "grep"

# 开机自启动
chkconfig --list | grep rsyslog
systemctl list-unit-files rsyslog.service

```

日志文件的格式包含以下 4 列：

- 事件产生的时间。
- 产生事件的服务器的主机名。
- 产生事件的服务名或程序名。
- 事件的具体信息。

```
/etc/rsyslog.conf

authpriv.* /var/log/secure
#服务名称[连接符号]日志等级 日志记录位置
#认证相关服务.所有日志等级 记录在/var/log/secure日志中

```

[https://blog.csdn.net/weixin_65690979/article/details/129003415](https://blog.csdn.net/weixin_65690979/article/details/129003415)