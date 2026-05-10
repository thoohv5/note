---
title: DFS&BFS
date: 2026-04-07
tags: [算法, 数据结构, 扩展]
type: reference
status: complete
source: https://mp.weixin.qq.com/s/PD1IYUhpRpbop4LkDnaAJA
---

## DFS 与 BFS

深度优先搜索 (DFS) 和广度优先搜索 (BFS) 是图和树遍历的两种基本算法。

### 核心区别

| 维度 | DFS | BFS |
|------|-----|-----|
| 数据结构 | 栈（递归/显式） | 队列 |
| 遍历顺序 | 深度优先 | 层级优先 |
| 最短路径 | 不保证 | 保证（无权图） |
| 空间复杂度 | O(h) h=深度 | O(w) w=宽度 |
| 适用场景 | 回溯、连通性 | 最短路径、层级遍历 |

### DFS 实现

```go
// 递归实现
func DFS(node *Node, visited map[*Node]bool) {
    if node == nil || visited[node] { return }
    visited[node] = true
    for _, child := range node.Children {
        DFS(child, visited)
    }
}

// 栈实现（迭代）
func DFSIterative(root *Node) {
    stack := []*Node{root}
    visited := map[*Node]bool{}
    for len(stack) > 0 {
        node := stack[len(stack)-1]; stack = stack[:len(stack)-1]
        if visited[node] { continue }
        visited[node] = true
        stack = append(stack, node.Children...)
    }
}
```

### BFS 实现

```go
func BFS(root *Node) {
    queue := []*Node{root}
    visited := map[*Node]bool{root: true}
    for len(queue) > 0 {
        node := queue[0]; queue = queue[1:]
        for _, child := range node.Children {
            if !visited[child] {
                visited[child] = true
                queue = append(queue, child)
            }
        }
    }
}
```

### 典型应用

- **DFS**：全排列、组合、N皇后、岛屿数量、拓扑排序
- **BFS**：最短路径、二叉树层序遍历、社交网络N度好友
- **记忆化DFS** → 动态规划的一种实现方式

### 相关

- [[二叉树]] [[回溯算法]] [[动态规划-理论]]