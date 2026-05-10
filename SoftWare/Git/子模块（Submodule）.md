---
title: 子模块（Submodule）
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: reference
status: complete
---

## 子模块（Submodule）

## 概述

**Git Submodule** 是 Git 中用于管理子项目的强大功能。它允许我们将一个 Git 仓库作为另一个 Git 仓库的子模块进行管理，从而使项目结构更加清晰，代码维护更加方便。

## 操作

### 模块添加

```bash
## 初始化主项目
git init <主项目名称>

## 添加子模块
git submodule add <外部仓库的URL> <目标路径>

## 提交变更
git commit -m "添加只模块"

```

### 模块移除

```bash
## 移除子模块
git submodule deinit -f <子模块路径>
git rm -f <子模块路径>
rm -rf .git/modules/<子模块路径>

## 推送更新
git push

```

### 模块克隆

- **`-init`** 选项：如果子模块尚未初始化，则将其初始化。
- **`-recursive`** 选项：递归更新所有子模块，包括嵌套子模块。

```bash

## 初始化子模块
git submodule init
git submodule update

## 等同上命令
git submodule update --init

/**
* 1. 初始化所有未初始化的子模块
* 2. 递归更新所有嵌套子模块到最新提交
*/
git submodule update --init --recursive

## 克隆子模块到单独的目录
git submodule clone <外部仓库的URL> <目标路径>
```

### 模块更新

```bash
## 递归更新
git submodule update --recursive

## 关联子模块到其父仓库最新提交
git submodule update --remote
```

### 模块变更

```bash
cd <子模块目录>
git add .
git commit -m "更新子模块 lib"
cd .. # 主模块目录
git submodule add lib
git commit -m "更新子模块版本"

```

### 分支管理

### 切换分支

```bash
git submodule checkout <分支名称>
```

### 创建分支

```bash
git submodule branch <分支名称>
```

### 合并分支

```bash
git submodule merge <分支名称>
```

## 附录

https://blog.csdn.net/yuxiatongzhi/article/details/138115350