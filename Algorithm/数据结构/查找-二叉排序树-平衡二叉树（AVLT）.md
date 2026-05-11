---
title: 平衡二叉树（AVLT）
date: 2026-04-07
tags: [算法, 数据结构]
type: reference
status: complete
source: "https://www.cs.usfca.edu/~galles/visualization/AVLtree.html"
---

## 平衡二叉树（AVLT）

### 概念

平衡二叉树是一种**二叉排序树**，其中每一个节点的左子树和右子树的高度差至多等于 1。平衡因子 BF = 左子树深度 − 右子树深度，AVL 树要求所有节点 |BF| ≤ 1。

### 旋转操作

插入/删除导致不平衡时，通过旋转恢复平衡：

| 类型 | 失衡节点 BF | 操作 |
|------|-----------|------|
| LL 型 | +2, 左子 +1 | 右旋 |
| RR 型 | -2, 右子 -1 | 左旋 |
| LR 型 | +2, 左子 -1 | 先左旋再右旋 |
| RL 型 | -2, 右子 +1 | 先右旋再左旋 |

### 复杂度

- 查找：O(log n)
- 插入：O(log n)，最多 1 次旋转
- 删除：O(log n)，可能需要 O(log n) 次旋转

### 对比

| 特性 | AVL | 红黑树 |
|------|-----|--------|
| 平衡条件 | 严格 \|BF\|≤1 | 颜色约束（较宽松） |
| 查找性能 | 略优 | 略差 |
| 插入/删除 | 旋转较多 | 旋转较少 |
| 适用场景 | 读多写少 | 读写均衡 |

### 可视化

[AVL Tree Visualizer](https://www.cs.usfca.edu/~galles/visualization/AVLtree.html)

### 相关笔记

- [[二叉排序树（BST）]]
- [[红黑树（RBT）]]
- [[查找-查找概述]]