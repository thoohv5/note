---
title: sudo
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: incomplete
---

## sudo

```bash

chmod 740 /etc/sudoers

vim /etc/sudoers

chmod 440 /etc/sudoers

```

### /etc/sudoers 权限管理的通用格式

```bash
被授权用户 授权计算机=(授权用户) 授权命令

ex:
	# 授权根用户在所有计算机上以所有用户的身份运行所有文件
	root ALL=(ALL) ALL
	# 授权toml用户组全部成员在所有计算机上以所有用户的身份运行useradd,userdel命令，且运行时不必输入密码
	%toml ALL=(ALL) NOPASSWD:usereadd,userdel

```