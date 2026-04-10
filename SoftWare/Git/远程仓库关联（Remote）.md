---
title: 远程仓库关联（Remote）
date: 2026-04-07
tags: [软件工具, Git]
type: note
status: incomplete
---

# 远程仓库关联（Remote）

```bash
# 查看
git remote -v
# 删除
git remote rm origin
# 添加
git remote add origin git@github.com:xxx.git

git checkout -b branch_name
git push --set-upstream origin branch_name
git checkout -t branch_name

```