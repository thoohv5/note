---
title: mount
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: complete
---

## mount

## 命令

```
mount [-t vfstype] [-o options] device dir

```

### 其中

- `t vfstype` 指定文件系统的类型，通常不必指定。mount 会自动选择正确的类型。常用类型有：

```
光盘或光盘镜像：iso9660
DOS fat16文件系统：msdos
Windows 9x fat32文件系统：vfat
Windows NT ntfs文件系统：ntfs
Mount Windows文件网络共享：smbfs
UNIX(LINUX) 文件网络共享：nfs

```

- `o options` 主要用来描述设备或档案的挂接方式。常用的参数有：

```
loop：用来把一个文件当成硬盘分区挂接上系统
ro：采用只读方式挂接设备
rw：采用读写方式挂接设备
iocharset：指定访问文件系统所用字符集

```

`device` 要挂接(mount)的设备

`dir` 设备在系统上的挂接点(mount point)