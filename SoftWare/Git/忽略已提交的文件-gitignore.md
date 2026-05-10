---
title: 忽略已提交的文件（.gitignore）
date: 2026-04-07
tags:
  - 软件工具
  - Git
type: guide
status: complete
---

## 忽略已提交的文件（.gitignore）

## 操作

只针对已经commit过且有改动的文件 (因为rm的是cached列表中的文件, cached列表即修改列表)

```bash
git rm -r --cached <filename>
vim .gitignore
git add .
git commit -m "mark"
git push

git rm -r --cached -f .

```

## `.gitignore`

### **前言**

在[Git版本控制](https://so.csdn.net/so/search?q=Git版本控)B6&spm=1001.2101.3001.7020系统中，**.gitignore** 文件用于指定[Git](https://so.csdn.net/so/search?q=Git&spm=1001.2101.3001.7020)应该忽略哪些文件和目录。通过忽略不必要的文件，可以减少[代码库](https://so.csdn.net/so/search?q=代码)93&spm=1001.2101.3001.7020的混乱，提高代码管理的效率。

### **语法规则**

### **注释**

任何以井号`（#）`开头的行都会被认为是注释，Git会忽略这些行。

例如：# 这是一个git忽略文件

### **空行**

**空行会被Git忽略，可以用于增加文件的可读性。**

### **匹配文件名**

可以使用简单的文件名或路径匹配规则来指定要忽略的文件和目录。

例如：temp.txt 忽略临时文件

### **通配符**

1. **星号`（*）`**
    
    星号匹配零个或多个任意字符。不包含**目录本身**
    
    例如：*.log 忽略全部以.log结尾的文件，temp* 忽略以temp开头的文件
    
2. **问号`（?）`**
    
    问号匹配任意一个字符。
    
    例如：temp? 忽略以temp开头后面加一个字符的文件，类似temp1、temp2这样的
    
3. **方括号`（[]）`**
    1. 方括号匹配指定范围内的任意一个字符。
    
    例如：[a-c]* 忽略任何以a、b、c等字符开头的文件
    
4. **高级规则**
    1. **目录匹配**
        
        **在文件名后面加上斜杠`（/）`可以指定要忽略的目录。**
        
        例如：logs/ 即忽略 logs 文件夹，/other/logs/ 这样的目录也会忽略。
        
    2. **例外规则**
        
        在规则前加上感叹号`（!）`可以指定不忽略的文件或目录。这在处理嵌套目录时特别有用。
        
        例如：我们要忽略maven项目中编译文件输出目录 target/文件夹，但是在src下的包文件可能有target名称的目录，这时候我们使用 !**/src/main/**/target/ 来指定不忽略。
        
    3. **双星号`（**）`**
        
        双星号匹配任意数量的目录。
        
        例如：**/temp/*  忽略任何位置的临时文件目录
        

### 命令

```bash
## 可以查看为什么某个文件或目录被 Git 忽略，这可以帮助你调试 .gitignore 文件的问题
git check-ignore -v 文件路径 
```

## `.git/info/exclude`

`.gitignore`和`.git/info/exclude`都能实现相同的功能，能够让Git忽略它们标记的文件。最重要的区别就是`.gitignore`能够在团队成员中共享，因此当某个文件被公认为“无需版本控制”，那么最好就把它放在`.gitignore`文件中。而`.git/info/exclude`文件则是供个人专用的，仅当自己觉得这个文件不用版本控制时，才把它放在`.git/info/exclude`文件中。