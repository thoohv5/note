---
title: tmpfiles.d
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
source: http://www.jinbuguo.com/systemd/tmpfiles.d.html
---

## tmpfiles.d

### 配置路径

| 路径 | 优先级 | 说明 |
|------|--------|------|
| `/etc/tmpfiles.d/*.conf` | 最高 | 用户自定义 |
| `/run/tmpfiles.d/*.conf` | 中 | 运行时配置 |
| `/usr/lib/tmpfiles.d/*.conf` | 最低 | 系统默认 |

### 常用配置类型

```ini
# 创建目录（不存在则创建）
d /run/myservice 0755 root root -

# 创建文件（不存在则创建）
f /etc/motd 0644 root root - "Welcome"

# 清理过期文件
D /tmp/mycache 0700 root root 7d

# 创建命名管道
p /run/myservice.pipe 0600 root root -
```

### 参考

- [tmpfiles.d 中文手册](http://www.jinbuguo.com/systemd/tmpfiles.d.html)
- [[systemd]]