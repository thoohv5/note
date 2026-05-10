---
title: ARRAY
date: 2026-04-07
tags:
  - 编程语言
  - Shell
type: note
status: complete
---

## ARRAY

```bash
Arr1=('Apple' 'Banana' 'Orange')

Arr2=(foo{1..2}) # => foo1 foo2
Arr3=({A..D})    # => A B C D

## 合并 => foo1 foo2 A B C D
Arr4=(${Arr1[@]} ${Arr3[@]})

## 声明构造
declare -a Numbers=(1 2 3)
Arr5+=(4 5) # 附加 => 1 2 3 4 5
```

```bash
${Fruits[0]}	第一个元素
${Fruits[-1]}	最后一个元素
${Fruits[*]}	所有元素
${Fruits[@]}	所有元素
${#Fruits[@]}	总数
${#Fruits}	第一节长度
${#Fruits[3]}	第n个长度
${Fruits[@]:3:2}	范围
${!Fruits[@]} 所有KEY
```