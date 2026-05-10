---
title: fdisk
date: 2026-04-07
tags:
  - 基础设施
  - Linux
  - General
  - 命令
type: reference
status: complete
---

## fdisk

## 概述

fdisk, Partition table manipulator for Linux, 用于管理磁盘的分区信息

## 命令

[[fdisk]]
[[Infrastructure/Linux/General/常用命令（Command）/其他常用命令/mkfs ext4|mkfs.ext4]]

```
## 查看分区
ll /dev/sd*

## 查看硬盘分区
fdisk -l

## 格式化磁盘
mkfs.ext4 /dev/sd[a/b]

```

```
## 分区
fdisk /dev/sd[a/b]

```

| 参数 | 含义 |
| --- | --- |
| b edit bsd disklabel | 编辑BSD磁盘标签 |
| c toggle the dos compatibility flag | 切换DOS兼容性标志 |
| d delete a partition | 删除分区 |
| g create a new empty GPT partition table | 创建新的空GPT分区表 |
| G create an IRIX (SGI) partition table | 创建IRIX(SGI)分区表 |
| l list known partition types | 显示分区类型 |
| m print this menu | 打印帮助菜单 |
| **n add a new partition** | 添加新的分区 |
| o create a new empty DOS partition table | 创建新的空DOS分区表 |
| p print the partition table | 显示分区表 |
| q quit without saving changes | 不保存，退出 |
| s create a new empty Sun disklabel | 创建新的空Sun磁盘标签 |
| t change a partition's system id | 改变分区类型 |
| u change display/entry units | 更改显示/输入单位 |
| v verify the partition table | 验证分区表 |
| **w write table to disk and exit** | 写分区表信息到硬盘，保存操作并退出 |
| x extra functionality (experts only) | 额外功能(仅限专家) |

## [[磁盘分区]]

## `df -h` 和 `fdisk -l`

`df -h`

- **作用：** 用于显示文件系统的磁盘空间使用情况。
- **输出：** 列出已挂载的文件系统的信息，包括每个文件系统的总大小、已用空间、可用空间和挂载点。
`fdisk -l`
- **作用：** 用于显示系统上所有硬盘分区的详细信息，包括分区类型、起始和结束扇区等。
- **输出：** 列出硬盘上所有分区的详细信息，但不提供关于文件系统使用情况的具体信息。
