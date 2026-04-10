---
title: 联合文件系统（UnionFS）
date: 2026-04-07
tags: [基础设施, Docker]
type: note
status: incomplete
---

# 联合文件系统（UnionFS）

`Union` 文件系统（如 OverlayFS）支持将多个目录挂载到同一目录，Docker 镜像就构建于此之上。

- 每一层都是只读的增量文件系统
- 最上层是可写层（容器运行时）
- 镜像层复用，实现快速构建与启动