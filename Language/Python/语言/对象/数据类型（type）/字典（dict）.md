---
title: 字典（dict）
date: 2026-04-07
  - 编程语言
  - Python
type: note
status: incomplete
---

## 字典（dict）

- `dict` 是一个“键-值”对(key-value pairs)的无序集合
- `dict` 中的每个元素都以“键:值(key:value)”的形式存储

```python
my_dict = {1:'value','key':2,'hello':[4,7]}

print(type(my_dict))    # <class 'dict'>
print(my_dict)          # {1: 'value', 'key': 2, 'hello': [4, 7]}
print(my_dict[1])       # value
print(my_dict['key'])   # 2
```