---
title: Cache & Buffer
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: complete
---

## Cache & Buffer

Page Cache和Buffer cache的区别

简单的讲，page cache用来缓存文件数据，buffer cache用来缓存磁盘数据。再有文件系统的情况下，对文件操作，那么数据会缓存到page cache，如果直接采用dd等工具对磁盘进行读写，那么数据会缓存到Bufer cache。

Buffer(buffer cache)以块形式缓冲了块设备的操作，定时或手动的同步到硬盘，它是为了缓冲写操作然后一次性将很多改动写入硬盘，避免频繁写硬盘，提高写入效率。

Page(Page Cache)以页面形式缓存了文件系统的文件，给需要使用的程序读取，它是为了给读操作提供缓冲，避免频繁读硬盘，提高读取效率