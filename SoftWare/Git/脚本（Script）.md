---
title: 脚本（Script）
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: note
status: incomplete
---

## 脚本（Script）

### 当前分支名

```
## 当前分支名
git rev-parse --abbrev-ref HEAD
```

### ssh替换http

```bash
## ssh替换http
git config --global --add url."ssh://git@git.zdns.cn:2222/".insteadOf "<https://git.zdns.cn/>"

```

### 判断分支

```
## 检查本地分支是否存在
if git rev-parse --verify <branch_name>;then
	存在该分支
else
	不存在该分支
fi

```