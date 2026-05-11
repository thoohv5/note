---
title: 为什么我必须在GIT LOG结尾处打Q
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: note
status: complete
source: https://www.dovov.com/git-logq.html
---

## 为什么我必须在GIT LOG结尾处打Q

> `git log` 默认使用 `less` 作为分页器（pager），需要按 `q` 退出。

### 解决方案

```bash
# 临时：禁用分页器
git --no-pager log -n 20 --pretty=oneline

# 永久：配置 Git 默认不分页
git config --global core.pager cat

# 或设置每页输出量
git config --global core.pager 'less -FRSX'

# 仅对 log 命令禁用
git config --global pager.log false
```

### 常用分页器参数

| 参数 | 含义 |
|------|------|
| `-F` | 内容少于一页时不分页 |
| `-R` | 保留 ANSI 颜色 |
| `-S` | 不折行（左右滚动） |
| `-X` | 退出时不清屏 |

### 参考

- [为什么我必须在GIT LOG结尾处打Q](https://www.dovov.com/git-logq.html)
- [[Git]]
- [[Git日志格式]]