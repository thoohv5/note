---
title: Rsync
date: 2026-04-07
tags:
  - 软件工具
type: guide
status: complete
---

## Rsync

## 概念

### 工作方式

```bash
Local:
	rsync [OPTION...] SRC... [DEST]

Access via remote shell:
  Pull: rsync [OPTION...] [USER@]HOST:SRC... [DEST]
  Push: rsync [OPTION...] SRC... [USER@]HOST:DEST

Access via rsync daemon:
  Pull: rsync [OPTION...] [USER@]HOST::MODULE/SRC... [DEST]
        rsync [OPTION...] rsync://[USER@]HOST[:PORT]/MODULE/SRC... [DES]
  Push: rsync [OPTION...] SRC... [USER@]HOST::MODULE/DEST
        rsync [OPTION...] SRC... rsync://[USER@]HOST[:PORT]/MODULE/DEST

```

### 注意事项

1. Usages with just one SRC arg and no DEST arg will list the source files instead of copying. 只有一个 SRC arg 而没有 DEST arg 的用法将列出源文件而不是复制。
2. Rsync refers to the local side as the lqclientrq and the remote side as the lqserverrq. Don't confuse lqserverrq with an rsync daemon -- a daemon is always a server, but a server can be either a daemon or a remote-shell spawned process Rsync 将本地端称为 lqclientrq，将远程端称为 lqserverrq。不要将 lqserverrq 与 rsync 守护进程混淆——守护进程始终是服务器，但服务器既可以是守护进程，也可以是远程 shell 生成的进程。
3. Note that rsync must be installed on both the source and destination machines. 请注意，必须在源计算机和目标计算机上都安装 rsync。

### 作用

1. 通过Rsync可以实现增量备份
2. 配合计划任务，Rsync可以实现定时或间隔时间数据同步
3. 配合inotify，Rsync可以实现触发式的实时同步

## 部署

### 配置文件 `[/etc/rsyncd.conf](<https://linux.die.net/man/5/rsyncd.conf>)`

```bash
### 全局配置 ###
## 欢迎词，默认：无
motd file = /etc/rsyncd.motd
## 服务进程
pid file = /var/run/rsyncd.pid
## 服务端口，默认：873
port 873
## 服务地址，默认：0.0.0.0
adress 192.168.1.128

## 可以包括模块配置的参数

### 模块配置 ###
[common]
	# 模块描述
	comment = welcome to rsync module common
	# 模块路径 [MUST]
	path = /common
	# 设置最大的并发连接数
	max connections = 10
  # 是否只能下载，默认：true
  read only = true
	# 是否只能上传，默认：false
	write only = false
	# 认证用户, 多个用户逗号隔开
	auth users = tom,jerry
	# 认证用户的密码文件
	secrets file = /etc/rsyncd.secrets
	# 允许访问的地址段
	hosts allow = 192.168.1.0/24
	# 拒绝访问的地址(除开允许的外)
	host deny = *
	# 模块列表是否显示模块，默认：false
	list = false

	#一个包含格式定义符的字符串，可以使用的格式定义符如下所示：
	#%h 远程主机名
	#%a 远程IP地址
	#%l 文件长度字符数
	#%p 该次rsync会话的进程id
	#%o 操作类型："send"或"recv"
	#%f 文件名
	#%P 模块路径
	#%m 模块名
	#%t 当前时间
	#%u 认证的用户名(匿名时是null)
	#%b 实际传输的字节数
	#%c 当发送文件时，该字段记录该文件的校验码
	#默认log格式为："%o %h [%a] %m (%u) %f %l"，一般来说,在每行的头上会添加"%t [%p] "。在源代码中同时发布有一个叫
	#rsyncstats的perl脚本程序来统计这种格式的日志文件。
	# 是否开启传输日志功能
	transfer logging = yes
	# 日志文件路径
	log file = /var/log/rsyncd.log

```

### 密码文件 `/etc/rsyncd.secrets`

```bash
## 服务端
## user:password
tom:123456
jerry:123456

## 客户端
## password
123456

```

<aside> 💡 # 修改密码文件权限，必须为600，否则会报错 `chmod 600 /etc/rsyncd.secrets`

</aside>

### [参数说明](https://linux.die.net/man/1/rsync)

```bash
-v 显示Rsync过程中的详细信息
-P 显示文件的传输进度
-n 仅测试，而不实际传输，一般配合-vvv查看rsync是如何工作的

-a 归档模式，表示递归传输并保持文件的属性，相当于-rlptgoD 选项
-r 递归拷贝目录
-l 保留软链接
-p 保持文件的权限
-t 保持mtime属性，建议任何时间都加上该选项
-g 保持gid属性
-o 保持owner属性
-D 拷贝特殊文件和设备文件，相当于 –devices --specials 表示支持b,c,s,p类型的文件

-z 传输过程进行压缩，加快传输速率

--delete	  以src为主，对dest进行同步，多则删，少则补
--exclude   排除不需要的文件，类似于tar中的exclude选项

--existing  要求只更新目标端已存在的文件，目标端还不存在的文件不传输。注意，使用相对路径时如果上层目录不存在也不会传输。
--ignore-existing 要求只更新目标端不存在的文件。和"--existing"结合使用有特殊功能

-W whole-file	 rsync将不再使用增量传输，而是全量传输

--password-file 密码文件

```

```bash
rsync -avzP --password-file=/etc/rsync.passwd SRC... rsync://[USER@]HOST[:PORT]/MODULE/DEST

rsync -avz --delete --password-file=/etc/rsync.passwd SRC... rsync://[USER@]HOST[:PORT]/MODULE/DEST

```

[[cp scp rsync mv 拷贝常识问题]]

## 附录

[rsync(1) - Linux man page](https://linux.die.net/man/1/rsync)

[rsyncd.conf(5) - Linux man page](https://linux.die.net/man/5/rsyncd.conf)

[rsync算法原理和工作流程分析](https://www.cnblogs.com/f-ck-need-u/p/7226781.html)