---
title: PHPStorm
date: 2026-04-07
tags: [软件工具, PHPStorm]
type: guide
status: complete
---

## PHPStorm

### php xdebug

```bash
[Zend]
;zend_extension=xdebug.so
xdebug.idekey= PHPSTORM
;xdebug.remote_autostart =1
xdebug.remote_enable = 1
xdebug.remote_handler = dbgp
xdebug.remote_log= /var/log/php/xdebug.log
xdebug.remote_mode = req
;xdebug.remote_host = 172.22.0.1
xdebug.remote_host = host.docker.internal
xdebug.remote_port = 19001
;xdebug 是否根据请求地址，返回调试数据（适合php为多个开发环境情况）
xdebug.remote_connect_back = 0
```

### PHPStorm 配置

1. Preferences | Languages & Frameworks | PHP

![](PHPStorm/Untitled.png)

![](PHPStorm/Untitled201.png

2. Preferences | Languages & Frameworks | PHP | Debug

![](PHPStorm/Untitled202.png

3. Preferences | Languages & Frameworks | PHP | Debug | DBGp Proxy

![](PHPStorm/Untitled203.png

4. Preferences | Languages & Frameworks | PHP | Servers

![](PHPStorm/Untitled204.png