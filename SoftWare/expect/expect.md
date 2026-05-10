---
title: expect
date: 2026-04-07
tags:
  - 软件工具
type: note
status: complete
source: https://www.cnblogs.com/zhangmingcheng/p/7449776.html
---

## expect

### 概述

expect 是一个用于自动化交互式命令行程序的脚本工具。它基于 Tcl，常用于自动处理 SSH、FTP、passwd、telnet 等需要根据提示输入内容的命令。

### 基本工作方式

- `spawn`：启动一个需要交互的子进程。
- `expect`：等待子进程输出匹配指定内容。
- `send`：向子进程发送输入，通常以 `\r` 表示回车。
- `interact`：把控制权交还给终端，继续人工交互。
- `set timeout`：设置等待匹配输出的超时时间，单位为秒。

### 最小示例

```expect
#!/usr/bin/expect
set timeout 30
spawn ssh -l username 192.168.1.1
expect "password:"
send "password\r"
interact
```

执行流程：

1. 使用 `spawn` 启动 SSH 登录。
2. 使用 `expect` 等待密码提示。
3. 使用 `send` 输入密码并回车。
4. 使用 `interact` 保持登录后的交互终端。

### 自动执行命令示例

```expect
#!/usr/bin/expect -f

set loginuser "root"
set loginpass "password"
set ipaddr [lindex $argv 0]
set timeout 30
set prompt "]#|~]?"

spawn ssh $loginuser@$ipaddr
expect {
    -re "Are you sure you want to continue connecting" {
        send "yes\r"
        exp_continue
    }
    -re "assword:" {
        send "$loginpass\r"
    }
}

expect -re $prompt
send "df -h\r"
expect -re $prompt
send "free -m\r"
expect -re $prompt
send "uptime\r"
expect -re $prompt
send "exit\r"
expect eof
```

### 使用注意

- `spawn` 是 expect 内部命令，不是系统可执行文件，不能用 `which spawn` 查找。
- `send` 输入命令或密码时通常要追加 `\r`，否则目标程序可能一直等待回车。
- 自动化 SSH 密码登录存在安全风险，优先使用 SSH key；必须使用密码时要注意脚本权限和密钥管理。
- 交互提示可能因语言、系统发行版、Shell 配置不同而变化，复杂脚本应使用正则匹配。
- 用于批量运维时要显式处理超时、拒绝连接、认证失败等异常分支。

### 参考

- [Centos expect spawn、linux expect 用法](https://www.cnblogs.com/zhangmingcheng/p/7449776.html)

[[SoftWare/expect/关闭expect回显spawn|关闭expect回显spawn]]
