---
title: Centos
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: incomplete
---

# Centos

```bash
tar -C /usr/local -zxf go*.tar.gz

cat >> /etc/profile <<EOF
export GOPRIVATE=git.zdns.cn
export GOPROXY=https://goproxy.cn,direct
export GOROOT=/usr/local/go
export GOPATH=/var/www/zdns
export PATH=$PATH:/usr/local/go/bin:/var/www/zdns/bin
EOF
source /etc/profile
```

# 附录

[Go下载 - Go语言中文网 - Golang中文社区](https://studygolang.com/dl)