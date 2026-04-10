---
title: 列表（list）
date: 2026-04-07
tags: [编程语言, Python]
type: note
status: complete
---

# 列表（list）

## 定义

`Python` 提供了一些组合数据类型，用于将多个值组合在一起，其中，最常用的组合数据类型就 是列表(list)。通过将所有值写在一对方括号`[]`里并以逗号，隔开就可以创建一个`list`对象。列表中 的值(对象)称为列表中的数据元素。这些元素的**数据类型可以不同**。

```python
my_list =[2, 3.14, 8, 'python',[9,10],'hello']

print(type(my_list)) # <class 'list'>
```

## **访问**

可以用切片操作(slicing operator) 访问一个范围内的多个元素，其格式为:`list[start:end:step=1]`

表示查询从 start 到 end 之间(但不包含 end)间隔步长为 step 的元素。如果没有指定 step，则表 示step =1，即间隔步长为1;如果没有指定start，则表示从第1个元素(start=0)开始;如果没有指 定 end，则表示到最后 1 个元素。

```python
my_list =[2, 3.14, 8, 'python',[9,10],'hello']

# 通过向下标运算符[]访问元素
print(my_list[0])   # 2
print(my_list[3])   # python
print(my_list[-1])  # hello
print(my_list[-2])  # [9, 10]

# 通过向下标运算符[]传递起始位置和结束位置的两个下标，返回连续多个元素组成的子 列表(不包含结束位置的元素)
print(my_list[1:4]) # [3.14, 8, 'python']
print(my_list[1:])  # [3.14, 8, 'python', [9, 10], 'hello']
print(my_list[:4])  # [2, 3.14, 8, 'python']
print(my_list[:])   # [2, 3.14, 8, 'python', [9, 10], 'hello']
print(my_list[4:1:-1]) # [[9, 10], 'python', 8]
print(my_list[1::-1])  # [3.14, 2]
print(my_list[:4:-1])  # ['hello']
print(my_list[::-1])   # ['hello', [9, 10], 'python', 8, 3.14, 2]
```

`list` 是可变的(**mutable**)，可以通过下标(切片)修改其中的 1 个或多个元素。对切片的修改还有“插入”或“删除”的效果。

```python
my_list =[2, 3.14, 8, 'python',[9,10],'hello']

# list对象是可修改的(mutable)
my_list[2]='小白'
print(my_list) # [2, 3.14, '小白', 'python', [9, 10], 'hello']

# 为指定下标范围内的元素重新赋值，从而替换为另一个 list
my_list[2:5]=[10,25]
print(my_list) # [2, 3.14, 10, 25, 'hello']

# 为指定下标范围内的元素赋值一个空的 list[ ]，相当于删除这个范围内的元素
my_list[2:4]=[]
print(my_list) # [2, 3.14, 'hello']

del my_list[1]  # 删除1个元素
del my_list     # 删除整个list
```

## **包含和遍历**

### 用`in`可判断1个元素是否在1个`list`里

```python
my_list = ['p','r','o','b','l','e','m'] 
print('p' in my_list)
```

### 用 `for` 迭代遍历 1 个 `list`

```python
for fruit in ['apple', 'banana', 'mango']:
    print("I like", fruit)
```

### 通过加法运算`+`拼接两个 `list`，用整数和 `list` 的乘法运算`*`复制 `list` 内容

```python
odd = [1, 3, 5]
print(odd + [9, 7, 5])  # [1, 3, 5, 9, 7, 5]
print(["re"] * 3)       # ['re', 're', 're']
```

## 内置函数

### 枚举函数 enumerate()

对于一个可迭代的(iterable)的对象(如列表、字符串)， 枚举函数 enumerate()将其组成一个索引序列，即一个“索引和值”的序列

```python
my_list =[2, 3.14, 8, 'python',[9,10],'hello']

for i,value in enumerate(my_list):
    print(i ,value)
```

### 排序函数sorted()

内置函数 sorted()可以用来对一个 list 排序，但它不修改原来的 list 而是返回一个排好序的新的

list。格式为:`sorted(iterable, key=None, reverse=False)`

```python
my_list =[2, 3.14, 8, 12,1,22]

print(sorted(my_list, reverse=True)) # [22, 12, 8, 3.14, 2, 1]
```

- `all()` 当list为空或所有元素为True时返回True，否则返回False。
- `any()` 当list不为空且有一个元素为True时返回True，否则返回False。
- `enumerate()` 返回一个enumerate对象，包含了所有元素的索引和值tuple。
- `len()` 返回list的长度(数据元素的个数)。
- `list()` 将一个可迭代对象(tuple、set、dict)转换为一个list。
- `max()` 返回list中的最大值元素。
- `min()` 返回list中的最小值元素。
- `sorted()` 返回新的排好序的list(原来的list不改变)。
- `sum()` 返回list中所有元素的和。

## 成员方法

- `append()` 将一个元素加到list的最后。
- `extend()` 将一个list中所有元素加到另外一个list的后面。
- `insert()` 在指定下标(索引)元素处插入一个元素。
- `remove()` 从list里删除一个元素。
- `pop()` 删除并返回指定索引位置的元素。
- `clear()` 删除list里的所有元素。
- `index()`返回匹配元素的索引。
- `count()` 返回元素在list里出现的次数。
- `sort()`以递增方式对list元素排序。
- `reverse()` 将list里的元素逆序。
- `copy()` 返回list的一个浅拷贝。