---
title: kube-proxy
date: 2026-04-07
tags: [基础设施, K8s, 架构]
type: reference
status: complete
---

## kube-proxy

## 概述

负责服务发现和负载均衡

负责为 Service 提供 cluster 内部的服务发现和负载均衡；

运行在每个Node 上的kube-proxy 进程其实就是一个智 能的软件负载均衡器，它负责把对 Service 的请求转发到后端的某个Pod 实例上，并在内部实现 服务的负载均衡与会话保持机制。

### 代理模式

**`userpace`**

`userspace`是指Linux操作系统的用户空间。在这种模型中，kube-proxy负责跟踪API Server上Service和Endpoints对象的变动（创建或移除），并据此调整Service资源的定义。对于每个Service对象，它会随机打开一个本地端口（运行于用户空间的kube-proxy进程负责监听），任何到达此代理端口的连接请求都将被代理至当前Service资源后端的各Pod对象，至于哪个Pod对象会被选中则取决于当前Service资源的调度方式，默认调度算法是轮询（round-robin）。userspace代理模型工作逻辑如图所示。另外，此类Service对象还会创建iptables规则以捕获任何到达ClusterIP和端口的流量。在Kubernetes 1.1版本之前，userspace是默认的代理模型。

**`iptables`**

Service对象的操作会触发集群中的每个kube-proxy并将其转换为定义在所属节点上的iptables规则，用于转发工作接口接收到的、与此Service资源ClusterIP和端口相关的流量。客户端发来请求将直接由相关的iptables规则进行目标地址转换（DNAT）后根据算法调度并转发至集群内的Pod对象之上，而无须再经由kube-proxy进程进行处理，因而称为iptables代理模型，如图所示。对于每个Endpoints对象，Service资源会为其创建iptables规则并指向其iptables地址和端口，而流量转发到多个Endpoint对象之上的默认调度机制是随机算法。iptables代理模型由Kubernetes v1.1版本引入，并于v1.2版本成为默认的类型。

**`ipvs`**

Kubernetes自v1.9版本起引入ipvs代理模型，且自v1.11版本起成为默认设置。在此种模型中，kube-proxy跟踪API Server上Service和Endpoints对象的变动，并据此来调用netlink接口创建或变更ipvs（NAT）规则，如图所示。它与iptables规则的不同之处仅在于客户端请求流量的调度功能由ipvs实现，余下的其他功能仍由iptables完成。