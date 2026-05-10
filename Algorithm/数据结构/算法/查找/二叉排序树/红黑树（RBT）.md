---
title: 红黑树（RBT）
date: 2026-04-07
tags:
  - 算法
  - 数据结构
type: note
status: complete
---

## 红黑树（RBT）

红黑树是一种含有红黑结点并能**自平衡的二叉查找树**。它必须满足下面性质：

- 性质1：每个节点要么是黑色，要么是红色。
- 性质2：根节点是黑色。
- 性质3：每个叶子节点（NIL）是黑色。
- 性质4：每个红色结点的两个子结点一定都是黑色。
- **性质5：任意一结点到每个叶子结点的路径都包含数量相同的黑结点。**

[Red/Black Tree](https://www.cs.usfca.edu/~galles/visualization/RedBlack.html)

平衡旋转

- 父亲结点为红色，叔叔结点为黑色

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151346176.png)

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151346820.png)

- 父亲结点为红色，叔叔结点为红色：父亲结点和叔叔结点变成黑色

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151347010.png)

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151347004.png)

- 父亲结点为黑色，祖父结点为红色，曾祖父结点为红色

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151348649.png)

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151348044.png)

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151349036.png)

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401151349355.png)

### 优劣性

红黑树与AVL的比较：AVL是严格平衡树，因此在增加或者删除节点的时候，根据不同情况，旋转的次数比红黑树要多；红黑是用非严格的平衡来换取增删节点时候旋转次数的降低；所以简单说，如果你的应用中，**搜索的次数远远大于插入和删除，那么选择AVL，如果搜索，插入删除次数几乎差不多，应该选择RB**。

[我终于把红黑树撕明白了](https://mp.weixin.qq.com/s/5F3-a9wOvsH-vfcBUTo1yA)