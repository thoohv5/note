---
title: mv 移动文件夹不能移动目标路径中已存在的文件夹
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: guide
status: complete
---

## mv 移动文件夹不能移动目标路径中已存在的文件夹

### 使用rsync 代替mv

```bash
rsync -a source/ target
rm -rf source/
```

`rsync`相当于从源到目的拷贝文件，而且可以设置参数，保持很多文件和文件夹的属性。对于拷贝文件实现备份，`rsync`总体而言比`cp`要好，因为`rsync`只拷贝那些改动了的内容。但这也不是绝对的，比如`cp -u`也可以实现同样的效果。

[[linux] mv: cannot move $ to $: Directory not empty_weixin_30872733的博客-CSDN博客](https://blog.csdn.net/weixin_30872733/article/details/102288030)