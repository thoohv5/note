---
title: Github
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: note
status: complete
source: https://github.com
---

## Github

> 基于 Git 的代码托管与协作平台。

### 地址

[https://github.com/thoohv5](https://github.com/thoohv5)

### 常用功能

| 功能 | 说明 |
|------|------|
| Repository | 代码仓库 |
| Issues | 任务跟踪和 Bug 管理 |
| Pull Request | 代码审查与合并 |
| Actions | CI/CD 自动化工作流 |
| Gist | 代码片段分享 |
| Wiki | 项目文档 |
| Projects | 看板项目管理 |

### 常用操作

```bash
# 克隆仓库
git clone git@github.com:user/repo.git

# Fork 后添加上游
git remote add upstream git@github.com:original/repo.git

# 创建 PR 分支
git checkout -b feature/xxx
git push origin feature/xxx
```

### 相关笔记

- [[Git]]
- [[Git流（Flow）]]
- [[变基（Rebase）]] 

由于`slug`地址这一栏填写的是外链，从而这个页面会跳转到外部网站