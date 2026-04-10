---
title: 元组（tuple）
date: 2026-04-07
tags: [编程语言, Python]
type: note
status: incomplete
---

# 元组（tuple）

- 元组(`tuple`)也是一个有序序列，也就是每个元素也有唯一的下标，**但 `tuple` 是不可修改的(immutable)**，另外，**定义 tuple 时用圆括号而不是方括号**

```python
my_tuple = (12)
print(type(my_tuple)) # <class 'int'>

my_tuple = (12,)
print(type(my_tuple)) # <class 'tuple'>
```