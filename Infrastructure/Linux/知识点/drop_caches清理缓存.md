# Linux shell：echo N ＞ /proc/sys/vm/drop_caches清理缓存-CSDN博客

URL: https://blog.csdn.net/weixin_44498318/article/details/120028843

### 

echo N>/proc/sys/vm/drop_caches清理缓存

**引言：**在[内核窗口—proc文件系统](http://blog.csdn.net/XD_hebuters/article/details/79593892)这篇笔记中，我们介绍了/proc主要体现进程及内核信息状态。与/proc下其它文件的“只读”属性不同的是，管理员可对/proc/sys子目录的许多文件内容进行修改，以更改内核的运行特性。写入操作一般类似于 echo DATA >/path/to/your/filename的格式进行。这里以echo N>/proc/sys/vm/drop_caches为例介绍说明。

### 一、问题现象：

**echo N > /proc/sys/vm/drop_caches , 当N数值不同时，free –m存在差异**

### 1.1、echo写入数值之前—free-m内容

首先查看/proc/sys/vm/drop_caches的默认值，以便可以修改回来

[](https://img-blog.csdn.net/20180319101851892?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1hEX2hlYnV0ZXJz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图1、/proc/sys/vm/drop_caches默认值

然后free-m查看此时内存使用情况，对于各字段含义，可以参考：[linux free命令使用](http://blog.csdn.net/xd_hebuters/article/details/79203098)。

[](https://img-blog.csdn.net/20180319101906201?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1hEX2hlYnV0ZXJz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图2、未更改数据时free -m内容

### 1.2、echo写入不同数据—free-m内容

执行echo 1 > /proc/sys/vm/drop_caches， 其中绿色框是标记发生改变的部分，由于buffers 和 cached数值变化，第一行free和used均发生变化。

[](https://img-blog.csdn.net/20180319101918734?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1hEX2hlYnV0ZXJz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图3、echo 1 > /proc/sys/vm/drop_caches

执行echo 2 > /proc/sys/vm/drop_caches， 其中绿色框是标记发生改变的部分，这次仅有buffers由0变为1，cached基本没有变化。

[](https://img-blog.csdn.net/20180319101929974?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1hEX2hlYnV0ZXJz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图4、echo 2 > /proc/sys/vm/drop_caches

执行echo 3 > /proc/sys/vm/drop_caches， 这次基本上，和写入2区别不大，没有什么变化。

[](https://img-blog.csdn.net/20180319101941679?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1hEX2hlYnV0ZXJz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图5、echo 3 > /proc/sys/vm/drop_caches

### 二、现象解释：

  前面我们执行echo N > /proc/sys/vm/drop_caches，再free –m查看内存使用情况，输入不同的N值，free –m内存中缓存buffers会有差异，现在我们就是要解释/proc/sys/vm、drop_caches， 这个文件到底是什么？另外可以写入哪些数值，具体适用于什么场景。

### 2.1、内核文档说明

  查看[linux内核文档](https://www.kernel.org/doc/Documentation/sysctl/vm.txt)，可以得到以下信息：

[](https://img-blog.csdn.net/20180319102226957?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1hEX2hlYnV0ZXJz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图6、linux内核文档关于drop_caches的说明

  可以看出，/proc/sys是一个虚拟文件系统，我们可以通过对它的读写操作做为与kernel实体间进行通信的一种手段。也就是说可以通过修改/proc中的文件，来对当前kernel的行为做出调整。那么我们可以通过调整/proc/sys/vm/drop_caches来释放内存。其默认数值为0.

  **向/proc/sys/vm/drop_caches中写入内容，会清理缓存。建议先执行sync**（sync 命令将所有未写的系统缓冲区写到磁盘中，包含已修改的 i-node、已延迟的块 I/O 和读写映射文件）。执行echo 1、2、3 至 /proc/sys/vm/drop_caches, 达到不同的清理目的。

### 2.2、使用建议

  这里找到一份关于清理缓存的使用建议[手工释放linux内存](http://blog.csdn.net/wyzxg/article/details/7279986/).

  实际项目中告诉我们，如果因为是应用有像内存泄露、溢出的问题，从swap的使用情况是可以比较快速可以判断的，但free上面反而比较难查看。相反，如果在这个时候，我们告诉用户，修改系统的一个值，“可以”释放内存，free就大了。用户会怎么想？不会觉得操作系统“有问题”吗？所以说，我觉得既然核心是可以快速清空buffer或cache，也不难做到（这从上面的操作中可以明显看到），但核心并没有这样做（默认值是0），我们就不应该随便去改变它。

  一般情况下，应用在系统上稳定运行了，free值也会保持在一个稳定值的，虽然看上去可能比较小。当发生内存不足、应用获取不到可用内存、OOM错误等问题时，还是更应该去分析应用方面的原因，如用户量太大导致内存不足、发生应用内存溢出等情况，否则，清空buffer，强制腾出free的大小，可能只是把问题给暂时屏蔽了。

  我觉得，排除内存不足的情况外，除非是在软件开发阶段，需要临时清掉buffer，以判断应用的内存使用情况；或应用已经不再提供支持，即使应用对内存的时候确实有问题，而且无法避免的情况下，才考虑定时清空buffer。（可惜，这样的应用通常都是运行在老的操作系统版本上，上面的操作也解决不了）。

### 三、小结：

  与/proc下其它文件的“只读”属性不同的是，管理员可对/proc/sys子目录的许多文件内容进行修改，以更改内核的运行特性。写入操作一般类似于 echo DATA >/path/to/your/filename的格式进行。本篇文章以echo N>/proc/sys/vm/drop_caches为例，介绍了写入不同数值，清理缓存的作用。

[](https://img-blog.csdn.net/20180319102249433?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L1hEX2hlYnV0ZXJz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

图7、管理员拥有写权限

参考资料：
1、[linux free命令使用](http://blog.csdn.net/xd_hebuters/article/details/79203098)
2、[linux内核文档](https://www.kernel.org/doc/Documentation/sysctl/vm.txt)
3、[手工释放linux内存](http://blog.csdn.net/wyzxg/article/details/7279986/).

纠错与建议
邮箱：[db_hebut@163.com](mailto:db_hebut@163.com)

### 版权声明

原文链接：[https://blog.csdn.net/XD_hebuters/article/details/79608136](https://blog.csdn.net/XD_hebuters/article/details/79608136)