---
title: GVM
date: 2026-04-07
tags: [编程语言, Golang, 环境]
type: note
status: incomplete
---

## GVM

```bash
## 安装gvm
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)

## 安装go
gvm install go1.4
gvm use go1.4 [--default]

## 卸载go
gvm implode 
(rm -rf ~/.gvm)
```

### 问题

```bash
SECURITY ERROR

rm -rf ~~
```

安装缓慢

```bash
vim ~/.gvm/scripts/install
GO_SOURCE_URL=git://github.com/golang/go
```

## 附录

[moovweb/gvm](https://github.com/moovweb/gvm)