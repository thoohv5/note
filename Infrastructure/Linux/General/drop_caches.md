---
title: drop_caches
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: complete
---

# drop_caches

Kernels 2.6.16 and newer provide a mechanism to have the kernel drop the page cache and/or inode and dentry caches on command, which can help free up a lot of memory. Now you can throw away that script that allocated a ton of memory just to get rid of the cache...

在清除buffers 前，为了防止数据丢失所以我们最好正常关机或者多执行几次sync命令，让位于buffer上的数据立刻写到磁盘里。

## 临时修改

重启会失效

### 清除pagecache

```
echo 1 > /proc/sys/vm/drop_caches

```

### 清除回收slab

分配器中的对象（包括目录项缓存和 inode 缓存

```
echo 2 > /proc/sys/vm/drop_caches

```

### 清除pagecache和slab分配器中的缓存对象

```
echo 3 > /proc/sys/vm/drop_caches

```

OR

```
sysctl -w vm.drop_caches=1

```

## 永久修改

重启后不失效

```
echo "vm.drop_caches = 1" >> /etc/sysctl.conf
sysctl -p /etc/sysctl.conf

```

# 附录

[https://linux-mm.org/Drop_Caches](https://linux-mm.org/Drop_Caches)[https://www.kernel.org/doc/Documentation/sysctl/vm.txt](https://www.kernel.org/doc/Documentation/sysctl/vm.txt)