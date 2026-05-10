---
title: 合并（Merge & Rebase）
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: note
status: complete
---

## 合并（Merge & Rebase）

### 命令

```bash
## 合并指定分支到当前分支[Fast forward]
git merge [--no-ff] <name> -m mark

## 合并变基
git rebase <name>
```

### 场景

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202312081113586.png)

### 合并

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202312081114628.png)

### 变基

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202312081114790.png)

### 交互式变基

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202312081114909.png)

## 附录

[https://www.atlassian.com/git/tutorials/merging-vs-rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)