---
title: 集合（set）
date: 2026-04-07
tags: [编程语言, Python, 语言]
type: note
status: complete
---

## 集合（set）

- `set` 是不包含重复元素的无序集合。set 是用左右花括号`{}`，包围的，以逗号隔开的一组元素
- 因为集合无序，所以**不能用下标操作其中的元素**
- set 中**不能有相同值的元素**
- 集合是根据其元素的哈希值存储元素的，所以无法计算哈希值的对象不能作为集合的元素
    - list是一个无法哈希的数据类型

```python
my_set = {2,2, 3,'python',8}
print(type(my_set)) # <class 'set'>
print(my_set)       # {8, 2, 3, 'python'}
```