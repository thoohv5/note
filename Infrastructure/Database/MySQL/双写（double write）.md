---
title: 双写（double write）
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: complete
---

# 双写（double write）

# 背景

磁盘最小单元 扇区 512B

文件系统最小单元 块 4K

InnoDB存储引擎最小单元 页 16K

![](https://s3.51cto.com/oss/202106/04/3ae2de41847a00eb56767d19a485f4ef.png)

# partial page wirte

在极端情况下（比如断电）往往并不能保证这一操作的原子性，16K的数据
，写入4K 时，发生了系统断电/os crash ，只有一部分写是成功的

为了解决 partial page write 问题 ，

当mysql将脏数据flush到data file的时候, 先使用memcopy 将脏数据复制到内存中的double write buffer ，
通过double write buffer再分2次，每次写入1MB到共享表空间，
然后马上调用fsync函数，同步到磁盘上，避免缓冲带来的问题。

# 两次写的作用

在数据库启动时（异常关闭的情况下），都会做数据库恢复（redo）操作，恢复的过程中，数据库都会检查页面是不是合法（校验等等），如果发现一个页面校验结果不一致，则此时会用到两次写这个功能，这个特点也正是为了处理这样的错误而设计的。
 此时的操作很明白了，将两次写的2个BLOCK（簇）都读出来，然后将所有这些页面写回到对应的页面中去，那么这时可以保证这些页面是正确的，并且是在写入前已经更新过的（最新数据）。在写回对应页面中去之后，那么就可以在这基础上继续做数据库恢复了，之后则不会再遇到这样的问题了，因为已经将最后有可能产生写断裂的数据页面都恢复了。

如果是写doublewrite buffer本身失败，那么这些数据不会被写到磁盘，InnoDB此时会从磁盘载入原始的数据，然后通过InnoDB的事务日志来计算出正确的数据，重新写入到doublewrite buffer。