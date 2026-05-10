---
title: free
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: complete
---

## free

[[buff_cache]]

## free 与 available

在 free 命令的输出中，有一个 free 列，同时还有一个 available 列。这二者到底有何区别？

free 是真正尚未被使用的物理内存数量。至于 available 就比较有意思了，它是从应用程序的角度看到的可用内存数量。Linux 内核为了提升磁盘操作的性能，会消耗一部分内存去缓存磁盘数据，就是我们介绍的 buffer 和 cache。所以对于内核来说，buffer 和 cache 都属于已经被使用的内存。当应用程序需要内存时，如果没有足够的 free 内存可以用，内核就会从 buffer 和 cache 中回收内存来满足应用程序的请求。所以从应用程序的角度来说，**available  = free + buffer + cache**。请注意，这只是一个很理想的计算方式，实际中的数据往往有较大的误差。

## 交换空间(swap space)

swap space 是磁盘上的一块区域，可以是一个分区，也可以是一个文件。所以具体的实现可以是 swap 分区也可以是 swap 文件。当系统物理内存吃紧时，Linux 会将内存中不常访问的数据保存到 swap 上，这样系统就有更多的物理内存为各个进程服务，而当系统需要访问 swap 上存储的内容时，再将 swap 上的数据加载到内存中，这就是常说的换出和换入。交换空间可以在一定程度上缓解内存不足的情况，但是它需要读写磁盘数据，所以性能不是很高。

现在的机器一般都不太缺内存，如果系统默认还是使用了 swap 是不是会拖累系统的性能？理论上是的，但实际上可能性并不是很大。并且内核提供了一个叫做 swappiness 的参数，用于配置需要将内存中不常用的数据移到 swap 中去的紧迫程度。这个参数的取值范围是 0～100，0 告诉内核尽可能的不要将内存数据移到 swap 中，也即只有在迫不得已的情况下才这么做，而 100 告诉内核只要有可能，尽量的将内存中不常访问的数据移到 swap 中。在 ubuntu 系统中，swappiness 的默认值是 60。如果我们觉着内存充足，可以在 /etc/sysctl.conf 文件中设置 swappiness：

vm.swappiness=10

如果系统的内存不足，则需要根据物理内存的大小来设置交换空间的大小。具体的策略网上有很丰富的资料，这里笔者不再赘述。

```
## centos 6
[root@localhost ~]# free -h
             total       used       free     shared    buffers     cached
Mem:          996M       521M       474M        56K        35M       118M
-/+ buffers/cache:       368M       628M
Swap:         1.5G       234M       1.2G

## centos 7.8> (7.2)
[root@localhost ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:           7.6G        947M        2.7G         18M        4.1G        6.4G

```

## /proc/meminfo 文件

其实 free 命令中的信息都来自于 /proc/meminfo 文件。/proc/meminfo 文件包含了更多更原始的信息，只是看起来不太直观：

```
/proc/meminfo

MemTotal:        8009048 kB
MemFree:         2500732 kB
MemAvailable:    6558760 kB
Buffers:          105636 kB
Cached:          4027992 kB
SwapCached:         2008 kB
Active:          3314568 kB
Inactive:        1793476 kB
Active(anon):     903320 kB
Inactive(anon):   101300 kB
Active(file):    2411248 kB
Inactive(file):  1692176 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:       8388604 kB
SwapFree:        8377928 kB
Dirty:              1032 kB
Writeback:             0 kB
AnonPages:        972412 kB
Mapped:            96824 kB
Shmem:             30200 kB
Slab:             287708 kB
SReclaimable:     260572 kB
SUnreclaim:        27136 kB
KernelStack:        3376 kB
PageTables:        10704 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:    12393128 kB
Committed_AS:    2003540 kB
VmallocTotal:   34359738367 kB
VmallocUsed:      157568 kB
VmallocChunk:   34359341052 kB
Percpu:              656 kB
HardwareCorrupted:     0 kB
AnonHugePages:     16384 kB
CmaTotal:              0 kB
CmaFree:               0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:       85952 kB
DirectMap2M:     3059712 kB
DirectMap1G:     7340032 kB

```

### total

物理内存总量，对应 `/proc/meminfo.MemTotal`

### free

空闲内存量，对应`/proc/meminfo.MemFree`

### buffers

内核缓冲区，对应`/proc/meminfo.Buffers`

### cached

文件缓冲页，对应`/proc/meminfo.Cached`

### slab

内核slab数据结构，对应`/proc/meminfo.Slab`

### cache

等同`free`的cache，即 cached以及slab之和

```
cache = cached + slab

```

### g_free

广义空闲内存（generalized free）

```
## buffers 和 cache是系统为了提升性能而使用的缓存，内存紧张时可随时回收另做他用。
g_free = free + buffers + cache

```

### active

活跃内存，`/proc/meminfo.Active`

活跃内存是指最近经常访问的内存，通常不会被重新分配

### inactive

非活跃内存，对应`/proc/meminfo.Inactive`

非活跃内存是指最新较少访问的内存，需要新分配内存时，这部分优先选择。

### available

可用内存，对应`/proc/meminfo.MemAvailable` [3.14内核版本]

可用内存值的是可用于启动一个新应用进程的内存，该指标是内核提供的一个预估值

## 说明

centos6系统内存使用：“-/+ buffers/cached”行的used和free作为参考
centos7系统内存使用：available

## 计算使用率

```
free | awk 'BEGIN{total=0;used=0;} (NR==2){total=$2; used=$3;} ($1=="-/+"){used=total-$4;} END{printf "%.2f\\n",used/total*100;}'

```