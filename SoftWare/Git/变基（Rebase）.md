---
title: 变基（Rebase）
date: 2026-04-07
  - 软件工具
  - Git
type: note
status: complete
---

## 变基（Rebase）

```bash
git rebase -i --root

pick：保留该commit（缩写:p）
reword：保留该commit，但我需要修改该commit的注释（缩写:r）
edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
squash：将该commit和前一个commit合并（缩写:s）
fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f） exec：执行shell命令（缩写:x）
drop：我要丢弃该commit（缩写:d）
```