---
title: inode
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: complete
---

# inode

硬盘的最小存储单位叫做"扇区"（Sector）,每个扇区储存512字节（相当于0.5KB）

文件存取的最小单位。"块"的大小，最常见的是4KB，即连续八个 sector组成一个 block

储存文件元信息的区域就叫做inode，中文译名为"索引节点”

inode信息

```bash
* 文件的字节数
* 文件拥有者的User ID
* 文件的Group ID
* 文件的读、写、执行权限
* 文件的时间戳，共有三个：ctime指inode上一次变动的时间，mtime指文件内容上一次变动的时间，atime指文件上一次打开的时间。
* 链接数，即有多少文件名指向这个inode
* 文件数据block的位置
```

查看inode信息

```bash
stat example.txt
```

inode号码

每个inode都有一个号码，操作系统用inode号码来识别不同的文件。

这里值得重复一遍，Unix/Linux系统内部不使用文件名，而使用inode号码来识别文件。对于系统来说，文件名只是inode号码便于识别的别称或者绰号。

表面上，用户通过文件名，打开文件。实际上，系统内部这个过程分成三步：首先，系统找到这个文件名对应的inode号码；其次，通过inode号码，获取inode信息；最后，根据inode信息，找到文件数据所在的block，读出数据。

[](https://www.ruanyifeng.com/blog/2011/12/inode.html)