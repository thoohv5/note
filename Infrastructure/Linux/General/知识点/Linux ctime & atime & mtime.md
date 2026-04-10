---
title: Linux ctime & atime & mtime
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: complete
---

# Linux ctime & atime & mtime

# 文件

文件的 Access time，atime 是在读取文件或者执行文件时更改的任何对inode的访问都会使此处改变。

文件的 Modified time，mtime 是在写入文件时随文件内容的更改而更改的。

文件的 Change time，ctime 是在写入文件、更改所有者、权限或链接设置时随 Inode 的内容更改而更改的。只要stat出来的内容发生改变就会发生改变。mtime的改变必然导致ctime的改变。指文件属性或文件位置改动的时间，如使**用chmod、chown、mv**指令**及ln**做文件的硬式连接，就会改变文件的Change time.

# 文件夹

文件夹的 Access time，atime 是在读取文件或者执行文件时更改的（我们只cd进入一个目录然后cd ..不会引起atime的改变，但ls一下就不同了）。

文件夹的 Modified time，mtime 是在文件夹中有文件的新建、删除才会改变（如果只是改变文件内容不会引起mtime的改变，换句话说如果ls -f <directory>的结果发生改变mtime就会被刷新。这里可能有人要争论了：我进入dd这个文件夹vi了一个文件然后退出，前后ls -f <directory>的结果没有改变但是文件夹的mtime发生改变了……这点请主意vi命令在编辑文件时会在本文件夹下产生一个".file.swp"临时文件，该文件随着vi的退出而被删除……这就导致了mtime的改变 [Auxten:p]不信你可以用nano修改文件来试验）。

文件夹的 Change time，ctime 基本同文件的ctime，其体现的是inode的change time。