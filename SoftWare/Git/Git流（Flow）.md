---
title: Git流（Flow）
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: note
status: complete
---

## 分支模型

Git Flow 是 Vincent Driessen 2010 年提出的分支管理模型，定义了严格的分支角色和合并流程。

### 主要分支

- `main`（master）：生产就绪代码，每次合并都是发布版本，打 tag
- `develop`：开发主线，feature 分支的合并目标

### 辅助分支

- `feature/*`：从 develop 分出，完成后合并回 develop
- `release/*`：从 develop 分出，用于发布准备（版本号、文档），完成后合并到 main 和 develop
- `hotfix/*`：从 main 分出紧急修复，完成后合并到 main 和 develop

### 工作流

```
main     ──●────────────●────────●──
            \          / \      /
develop  ────●──●──●──●───●────●──
              \  /  /     \  /
feature/*      ●──●         ●
                          release/*
```

## 使用场景

- 有固定发布周期的项目
- 多人协作，需求并行开发
- 需要严格版本管理

## 优缺点

| 优点 | 缺点 |
|------|------|
| 分支角色清晰 | 流程重，发布慢 |
| 支持紧急热修复 | 不适合持续部署 |
| 版本历史干净 | 长期 feature 分支合并冲突 |

## 其他分支模型

- **GitHub Flow**：main + feature 分支，适合持续部署
- **GitLab Flow**：环境分支（staging/pre-production/production）
- **Trunk-Based**：短周期向主干合并，配合 feature flag

## 参考

- [[SoftWare/Git/Git流（Flow）.md|Git Flow 原图]]
- [[SoftWare/Git/合并（Merge & Rebase）]]
- [[SoftWare/Git/提交日志规范]]
- [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)