---
title: item2
date: 2026-04-07
tags:
  - 软件工具
  - 开发工具
type: note
status: incomplete
---

## item2

> 安装 iTerm2
> 

```c
brew cask install iterm2
```

> iTerm2保持ssh连接不断开
> 

```bash
vim ~/.ssh/config

Host *
    ServerAliveInterval 60

```