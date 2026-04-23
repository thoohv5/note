---
title: 命名空间（Linux NameSpace）
date: 2026-04-07
  - 基础设施
  - Docker
type: reference
status: complete
---

## 命名空间（Linux NameSpace）

## 概念

namespace 是 Linux内核用来隔离内核资源的方式。通过 namespace 可以让一些进程只能看到与自己相关的一部分资源，而另外一些进程也只能看到与它们自己相关的资源，这两拨进程根本就感觉不到对方的存在。具体的实现方式是把一个或多个进程的相关资源指定在同一个 namespace 中。

Linux namespaces 是对全局系统资源的一种封装隔离，使得处于不同 namespace 的进程拥有独立的全局系统资源，改变一个 namespace 中的系统资源只会影响当前 namespace 里的进程，对其他 namespace 中的进程没有影响。

## 用途

Linux 内核实现 namespace 的一个主要目的就是实现轻量级虚拟化(容器)服务。在同一个 namespace 下的进程可以感知彼此的变化，而对外界的进程一无所知。这样就可以让容器中的进程产生错觉，认为自己置身于一个独立的系统中，从而达到隔离的目的。也就是说 linux 内核提供的 namespace 技术为 docker 等容器技术的出现和发展提供了基础条件。

我们可以从 docker 实现者的角度考虑该如何实现一个资源隔离的容器。比如是不是可以通过 chroot 命令切换根目录的挂载点，从而隔离文件系统。为了在分布式的环境下进行通信和定位，容器必须要有独立的 IP、端口和路由等，这就需要对网络进行隔离。同时容器还需要一个独立的主机名以便在网络中标识自己。接下来还需要进程间的通信、用户权限等的隔离。最后，运行在容器中的应用需要有进程号(PID)，自然也需要与宿主机中的 PID 进行隔离。也就是说这六种隔离能力是实现一个容器的基础

## 类型

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/linux_namespace.png)

### `IPC`

用于隔离进程间通讯所需的资源（ `System V IPC, POSIX message queues`），`PID`命名空间和IPC命名空间可以组合起来用，同一个IPC名字空间内的进程可以彼此看见，允许进行交互，不同空间进程无法交互

### `Network`

`Network Namespace`为进程提供了一个完全独立的网络协议栈的视图。包括网络设备接口，IPv4和IPv6协议栈，IP路由表，防火墙规则，`sockets`等等。一个`Network Namespace`提供了一份独立的网络环境，就跟一个独立的系统一样。

### `Mount`（`chroot`）

每个进程都存在于一个`mount Namespace`里面，`mount Namespace`为进程提供了一个文件层次视图。如果不设定这个flag，子进程和父进程将共享一个`mount Namespace`，其后子进程调用`mount`或`umount`将会影响到所有该`Namespace`内的进程。如果子进程在一个独立的`mount Namespace`里面，就可以调用`mount`或`umount`建立一份新的文件层次视图。

`Mount Namespace` 修改的，是容器进程对文件系统“挂载点”的认知。但是，这也就意味着，只有在“挂载”这个操作发生之后，进程的视图才会被改变。而在此之前，新创建的容器会直接继承宿主机的各个挂载点

`Mount Namespace` 跟其他 `Namespace` 的使用略有不同的地方：它对容器进程视图的改变，一定是伴随着挂载操作（`mount`）才能生效。

### `PID`

linux通过命名空间管理进程号，同一个进程，在不同的命名空间进程号不同！进程命名空间是一个父子结构，子空间对于父空间可见。

### `User`

用于隔离用户

### `UTS`

用于隔离主机名

### `Cgroup`