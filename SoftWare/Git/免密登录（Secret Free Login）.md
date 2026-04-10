---
title: 免密登录（Secret Free Login）
date: 2026-04-07
tags: [软件工具, Git]
type: note
status: complete
---

# 免密登录（Secret Free Login）

```bash
git config --global user.name thooh
git config --global user.email rol@thooh.com

git config --local user.name "张三"
git config --local user.email "zhansan@996icu.com"

cd ~/.ssh
ssh-keygen -t rsa -C "lirui@thooh.com"
cat ~/.ssh/id_rsa.pub
ssh-add id_rsa
ssh -T git@gitee.com

cd ～/.ssh
ssh-keygen -t rsa -C "lirui@zdns.cn"
cat ~/.ssh/id_rsa.pub
ssh-add id_rsa
ssh -T git@zdns.cn

```

```bash
# Could not open a connection to your authentication agent.
ssh-agent bash

# 启动 SSH 代理并设置环境变量
eval "$(ssh-agent -s)"

# 正确的权限
chmod 400 id_rsa

```