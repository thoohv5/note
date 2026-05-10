---
title: 认识BASH这个Shell
date: 2026-04-23
tags: [基础设施, Linux]
type: note
status: incomplete
---

## 认识BASH这个Shell

### 硬件、核心与 Shell

- Shell 是操作系统内核与用户之间的接口程序
- 用户通过 Shell 输入指令，内核通过 Shell 控制硬件

### 为何要学命令行？

- X Window 各发行版不通用，shell 命令跨发行版通用
- 远程管理时命令行传输更快、更稳定
- Shell 让你能深入了解 Linux 系统

### Linux 使用的 Shell

Linux 默认使用 **Bash (Bourne Again SHell)**，是 Bourne Shell 的增强版。

### 常见 Shell 类型

- sh (Bourne Shell)
- bash (Bourne Again Shell) - Linux 默认
- csh (C Shell)
- ksh (Korn Shell)

### 查看可用 Shell

```bash
cat /etc/shells
```

### Bash 的主要功能

### 1. 历史命令 (history)

- 按 `↑` `↓` 键调取之前输入的指令
- 记录文件: `~/.bash_history`
- 默认记录 1000 条

### 2. 命令补全 (Tab)

```bash
## 按 Tab 补全指令或文件名
ls -la /et<Tab>
```

### 3. 命令别名 (alias)

```bash
## 查看当前别名
alias

## 设置别名
alias lm='ls -al'
```

### 4. 作业控制 (job control)

- `[Ctrl]+Z`: 暂停当前工作
- `jobs`: 查看后台工作
- `bg`: 切换到后台
- `fg`: 切换到前台

### 使用 type 识别指令类型

```bash
type ls      # ls 是外部指令
type cd      # cd is a shell builtin (内置命令)
```

### 指令编辑快捷键

| 快捷键 | 功能 |
|--------|------|
| `Ctrl + C` | 终止当前指令 |
| `Ctrl + Z` | 暂停当前工作 |
| `Ctrl + D` | 结束输入 (EOF) |
| `Ctrl + L` | 清屏 |
| `Ctrl + U` | 删除整行 |
| `Ctrl + A` | 移动到行首 |
| `Ctrl + E` | 移动到行尾 |
| `Tab` | 命令/文件补全 |

### 跨行指令

使用 `\` 续行：

```bash
cp file1 file2 \
    file3 file4 \
    /backup/
```

> [!TIP]
> 反斜线 `\` 紧接在 Enter 之前，用于将长指令分行书写。