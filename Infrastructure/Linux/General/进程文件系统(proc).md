---
title: 进程文件系统(proc)
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: complete
---

## 进程文件系统(proc)

Linux系统上的/proc目录是一种文件系统，即proc文件系统

/proc是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，用户可以通过这些文件查看有关系统硬件及当前正在运行进程的信息，甚至可以通过更改其中某些文件来改变内核的运行状态。

### 相关文件

### 执行命令

```
/usr/local/appsys/normal/package/dhcp_ipplus/dhcp_ipplus server --conf /usr/local/appdata/normal/data/dhcp_ipplus/configs

```

### /proc/N/xxx

### cmdline

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251012682.png)

启动进程的完整命令，但僵尸进程目录中的此文件不包含任何信息

### cwd

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251013253.png)

指向进程运行目录的一个符号链接

### environ

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251015097.png)

进程的环境变量列表，彼此间用空字符（NULL）隔开；变量用大写字母表示，其值用小写字母表示；

### exe

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251011794.png)

指向启动进程的可执行文件（完整路径）的符号链接，通过/proc/N/exe可以启动当前进程的一个拷贝

### fd

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251551416.png)

目录，包含当前进程打开的每一个文件的文件描述符（file descriptor）,文件描述符是指向实际文件的一个符号链接

### limits

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251551756.png)

进程所使用的每一个受限支援的软限制、硬限制和管理单元；此文件仅可由实际启动当前进程UID用户读取；（2.6.24以上）

### maps

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251552935.png)

进程关联到的每个可执行文件和库文件在内存中的映射区域一级访问权限所组成的列表

### mem

进程所占用的内存空间，由open、read和lseek等系统调用使用，不能被用户读取；

### root

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251554383.png)

指向进程运行根目录的符号链接；在Unix和Linux系统上，通常采用chroor命令使每一个进程运行于独立的根目录。

### stat

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251555950.png)

进程的状态信息，包含一系统格式化后的数据列，可读性差，通常由ps命令使用；

### statm

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251555137.png)

进程占用内存的状态信息，通常以“界面”(page)表示。

### status

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251556429.png)

与stat所提供信息类似，但可读性较好，每行表示一个属性信息

### task

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251557370.png)

目录文件，包含由当前进程所运行的每一个线程的相关信息，每个线程的相关信息文件均保存在一个由线程号(tid)命令的目录中，类似于每个进程目录中的内容（2.6以上）

### /proc/apm

高级电源管理(APM)版本信息及电池相关状态信息，通常由apm命令使用

### /proc/buddyinfo

用于诊断内存碎片问题的相关信息文件

### /proc/cmdline

启动时传递至内核的相关参数信息，通常由lilo或grub等启动管理工具进行传递

### /proc/cpuinfo

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251614481.png)

处理器的相关信息的文件

### /proc/crypto

系统上已安装的内核使用的密码算法及每个算法的详细信息列表

### /proc/devices

系统已经加载的所有块设备和支付设备的信息，包含主设备号和设备组名

### /proc/diskstats

每块磁盘设备的磁盘I/O统计信息列表（2.5.69以上）

### /proc/dma

每个正在使用且注册的ISA DMA通道的信息列表

### /proc/execdomains

内核当前支持的执行域信息列表

### /proc/fb

帧缓冲设备列表文件，包含帧缓冲设备的设备号和相关驱动信息

### /proc/filesystems

当前被内核支持的文件系统类型列表文件，被标示为nodev的文件系统表示不需要块设备的支持；通常mount一个设备时，如果没有指定文件系统类型将通过此文件来决定其所需文件系统的类型；

### /proc/interrupts

X86或X86_64体系架构系统上每个IRQ相关的中断号列表；多路处理器平台上每个CPU对于每个I/O设备均有自己的中断号；

### /proc/iomem

每个物理设备上的记忆体（RAM或者ROM）在系统内存中的映射信息；

### /proc/ioports

当前正在使用且已经注册过的与物理设备进行通讯的输入-输出端口范围信息列表；如下面所示，第一列表示注册的I/O端口范围，其后表示相关的设备；

### /proc/kallsyms

模块管理工具用来动态链接或绑定可装载模块的符号定义，由内核输出；（内核2.5.71以后的版本支持此功能）；通常这个文件中的信息量相当大；

### /proc/kcore

系统使用的物理内存，以ELF核心文件（core file）格式存储，其文件大小为已使用的物理内存（RAM）加上4KB；这个文件用来检查内核数据结构的当前状态，因此，通常由GBD通常调试工具使用，但不能使用文件查看命令打开此文件；

### /proc/kmsg

此文件用来保存由内核输出的信息，通常由/sbin/klogd或/bin/dmsg等程序使用，不要试图使用查看命令打开此文件；

### /proc/loadavg

保存关于CPU和磁盘I/O的负载平均值，其前三列分别表示每1秒钟、每5秒钟及每15秒的负载平均值，类似于uptime命令输出的相关信息；第四列是由斜线隔开的两个数值，前者表示当前正由内核调度的实体（进程和线程）的数目，后者表示系统当前存活的内核调度实体的数目；第五列表示此文件被查看前最近一个由内核创建的进程的PID；

### /proc/locks

保存当前由内核锁定的文件的相关信息，包含内核内部的调试数据；每个锁定占据一行，且具有一个惟一的编号；如下输出信息中每行的第二列表示当前锁定使用的锁定类别，POSIX表示目前较新类型的文件锁，由lockf系统调用产生，FLOCK是传统的UNIX文件锁，由flock系统调用产生；第三列也通常由两种类型，ADVISORY表示不允许其他用户锁定此文件，但允许读取，MANDATORY表示此文件锁定期间不允许其他用户任何形式的访问；

### /proc/mdstat

保存RAID相关的多块磁盘的当前状态信息，在没有使用RAID机器上，其显示为如下状态：

### /proc/meminfo

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401251619800.png)

系统中关于当前内存的利用状况等的信息，常由free命令使用；可以使用文件查看命令直接读取此文件，其内容显示为两列，前者为统计属性，后者为对应的值；

### /proc/mounts

在内核2.4.29版本以前，此文件的内容为系统当前挂载的所有文件系统，在2.4.19以后的内核中引进了每个进程使用独立挂载名称空间的方式，此文件则随之变成了指向/proc/self/mounts（每个进程自身挂载名称空间中的所有挂载点列表）文件的符号链接；

### /proc/modules

当前装入内核的所有模块名称列表，可以由lsmod命令使用，也可以直接查看；如下所示，其中第一列表示模块名，第二列表示此模块占用内存空间大小，第三列表示此模块有多少实例被装入，第四列表示此模块依赖于其它哪些模块，第五列表示此模块的装载状态（Live：已经装入；Loading：正在装入；Unloading：正在卸载），第六列表示此模块在内核内存（kernel memory）中的偏移量；

### /proc/partitions

块设备每个分区的主设备号（major）和次设备号（minor）等信息，同时包括每个分区所包含的块（block）数目；

### /proc/pci

内核初始化时发现的所有PCI设备及其配置信息列表，其配置信息多为某PCI设备相关IRQ信息，可读性不高，可以用“/sbin/lspci –vb”命令获得较易理解的相关信息；在2.6内核以后，此文件已为/proc/bus/pci目录及其下的文件代替；

### /proc/slabinfo

在内核中频繁使用的对象（如inode、dentry等）都有自己的cache，即slab pool，而/proc/slabinfo文件列出了这些对象相关slap的信息；详情可以参见内核文档中slapinfo的手册页；

### /proc/stat

实时追踪自系统上次启动以来的多种统计信息；如下所示，其中，

“cpu”行后的八个值分别表示以1/100（jiffies）秒为单位的统计值（包括系统运行于用户模式、低优先级用户模式，运系统模式、空闲模式、I/O等待模式的时间等）；

“intr”行给出中断的信息，第一个为自系统启动以来，发生的所有的中断的次数；然后每个数对应一个特定的中断自系统启动以来所发生的次数；

“ctxt”给出了自系统启动以来CPU发生的上下文交换的次数。

“btime”给出了从系统启动到现在为止的时间，单位为秒；

“processes (total_forks) 自系统启动以来所创建的任务的个数目；

“procs_running”：当前运行队列的任务的数目；

“procs_blocked”：当前被阻塞的任务的数目；

```
[root@rhel5 ~]# more /proc/stat
cpu  2751 26 5771 266413 2555 99 411 0
cpu0 2751 26 5771 266413 2555 99 411 0
intr 2810179 2780489 67 0 3 3 0 5 0 1 0 0 0 1707 0 0 9620 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 5504 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 12781 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
ctxt 427300
btime 1234084100
processes 3491
procs_running 1
procs_blocked 0

```

### /proc/swaps

当前系统上的交换分区及其空间利用信息，如果有多个交换分区的话，则会每个交换分区的信息分别存储于/proc/swap目录中的单独文件中，而其优先级数字越低，被使用到的可能性越大；下面是作者系统中只有一个交换分区时的输出信息；

```
[root@rhel5 ~]# more /proc/swaps
Filename                                Type            Size    Used    Priority
/dev/sda8                               partition       642560  0       -1

```

### /proc/uptime

系统上次启动以来的运行时间，如下所示，其第一个数字表示系统运行时间，第二个数字表示系统空闲时间，单位是秒；

```
[root@rhel5 ~]# more /proc/uptime
3809.86 3714.13

```

### /proc/version

当前系统运行的内核版本号，在作者的RHEL5.3上还会显示系统安装的gcc版本，如下所示；

```
[root@rhel5 ~]# more /proc/version
Linux version 2.6.18-128.el5 ([email]mockbuild@hs20-bc1-5.build.redhat.com[/email]) (gcc version 4.1.2 20080704 (Red Hat 4.1.2-44)) #1 SMP Wed Dec 17 11:42:39 EST 2008

```

### /proc/vmstat

当前系统虚拟内存的多种统计数据，信息量可能会比较大，这因系统而有所不同，可读性较好；下面为作者机器上输出信息的一个片段；（2.6以后的内核支持此文件）

```
[root@rhel5 ~]# more /proc/vmstat
nr_anon_pages 22270
nr_mapped 8542
nr_file_pages 47706
nr_slab 4720
nr_page_table_pages 897
nr_dirty 21
nr_writeback 0
…………

```

### /proc/zoneinfo

内存区域（zone）的详细信息列表，信息量较大，下面列出的是一个输出片段：

```
[root@rhel5 ~]# more /proc/zoneinfo
Node 0, zone      DMA
  pages free     1208
        min      28
        low      35
        high     42
        active   439
        inactive 1139
        scanned  0 (a: 7 i: 30)
        spanned  4096
        present  4096
    nr_anon_pages 192
    nr_mapped    141
    nr_file_pages 1385
    nr_slab      253
    nr_page_table_pages 2
    nr_dirty     523
    nr_writeback 0
    nr_unstable  0
    nr_bounce    0
        protection: (0, 0, 296, 296)
  pagesets
  all_unreclaimable: 0
  prev_priority:     12
  start_pfn:         0

```

### /proc/sys

与/proc下其它文件的“只读”属性不同的是，管理员可对/proc/sys子目录中的许多文件内容进行修改以更改内核的运行特性，事先可以使用“ls -l”命令查看某文件是否“可写入”。写入操作通常使用类似于“echo DATA > /path/to/your/filename”的格式进行。需要注意的是，即使文件可写，其一般也不可以使用编辑器进行编辑