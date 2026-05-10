---
title: 重置（Reset）
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: note
status: incomplete
---

## 重置（Reset）

```bash
git reset [<mode>] [<commit>] <mode>
--mixed
版本库 => 工作区
暂存区 => 工作区
--soft
版本库 => 暂存区
--hard
版本库 => commit
暂存区 => commit
工作区 => commit
--keep
```