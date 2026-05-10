---
title: 恢复机制（RestartPolicy）
date: 2026-04-07
tags:
  - 基础设施
  - K8s
type: guide
status: complete
---

## 恢复机制（RestartPolicy）

### 参数

Always：在任何情况下，只要容器不在运行状态，就自动重启容器；

OnFailure: 只在容器 异常时才自动重启容器；

Never: 从来不重启容器。

### 注意

Pod 的恢复过程，永远都是发生在当前节点上，而不会跑到别的节点上去。事实上，一旦一个 Pod 与一个节点（Node）绑定，除非这个绑定发生了变化（pod.spec.node 字段被修改），否则它永远都不会离开这个节点。这也就意味着，如果这个宿主机宕机了，这个 Pod 也不会主动迁移到其他节点上去

### 设计原则

1. 只要 Pod 的 restartPolicy 指定的策略允许重启异常的容器（比如：Always），那么这个 Pod 就会保持 Running 状态，并进行容器重启。否则，Pod 就会进入 Failed 状态 。
2. 对于包含多个容器的 Pod，只有它里面**所有的容器**都进入异常状态后，Pod 才会进入 Failed 状态。在此之前，Pod 都是 Running 状态。