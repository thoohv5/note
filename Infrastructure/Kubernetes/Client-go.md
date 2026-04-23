---
title: Client-go
date: 2026-04-07
  - 基础设施
  - K8s
type: guide
status: complete
---

## Client-go

## 概述

![image.png](../../../Language/Golang/第三方包/K8S/kubernetes20client-go/image.png

### **整体流程简介**：

- Reflector会持续监听k8s集群中指定资源类型的API，当发现变动和更新时，就会创建一个发生变动的 **对象副本**，并将其添加到队列DeltaFIFO中
- Informer监听DeltaFIFO队列，取出对象，做两件事：
    - （1）将对象加入Indexer，Indexer 会将 **[对象key, 对象]** 存入一个线程安全的Cache中
    - （2）根据对象的 资源类型和操作，找到对应 Controller 预先提供的 Resource Event Handler，调用Handler，将对象的Key加入该 Controller 的 Workqueue
- Controller 的循环函数 ProcessItem，监听到 Workqueue 有数据了，就会取出一个key，交给处理函数Worker，Worker 会根据 Key，使用 Indexer reference 从 Cache 中 获取 该key对应的 真实对象。然后就可以进行调谐了。

### **注意点**

- DeltaFIFO 中 存的是 对象副本
- Cache 中 存的是 [对象key, 对象] 的映射
- Workqueue 中存的是 对象Key
- CRDController 中，使用Informer对象，是为了向其中添加一些 Resource Event Handlers
- CRDController 中，使用Indexer对象，是为了根据对象Key，获取对象实例

### **client-go组件**

- **Reflector**
    - reflector会一直监听kubernetes中指定资源类型的API，实现监听的函数就是ListAndWatch。这种监听机制既适用于k8s的内建资源，也适用于自定义资源。
    - 当reflector通过监听API发现资源对象实例存在新的 notification 时，它就会使用 listing API 获取这个新的实例对象，并将其放入 watchHandler 函数内的 DeltaFIFO 中；
- **Informer**
    - Informer 会从 Delta FIFO 中取出对象。实现这个功能的方法对应源码中的 processLoop；
    - Informer 取出对象后，根据Resource类型，调用对应的 Resource Event Handler 回调函数，该函数实际上由某个具体的 Controller 提供，函数中会获取对象的 key，并将 key 放入到 该Controller 内部的 Workqueue 中，等候处理。
- **Indexer 和 Thread Safe Store**
    - Indexer 会提供对象的索引功能，通常是基于对象Key来创建索引。默认索引函数是MetaNamespaceKeyFunc， 它生成的索引键为/格式。
    - Indexer 维护着一个线程安全的 Cache，即 **Thread Safe Store**。存储的是[对象key, 对象]，用对象Key可以进行获取对象实例。
- **Resource Event Handlers reference**
    - 这实际上是所有Controller的Resource Event Handlers的引用。
    - 这些 handlers 由具体的Controller提供，就是 Informer 的回调函数。Informer 会根据资源的类型，调用对应Controller的 handler 方法
    - handler 通常都是用于将资源对象的key放入到 该Controller 内部的 Workqueue 中，等候处理。

### **自定义控制器组件**

- **Informer reference**
    - Informer reference 是 Informer 实例对象的引用，用于操作和处理自定义资源对象
    - 我们编写自定义控制器时，需要引用自己需要的Informer，向其中加入一系列 Resource Event Handlers
- **Indexer reference**
    - Indexer reference 是 Indexer实例对象的引用，用于根据对象Key索引资源对象
    - 我们编写自定义控制器时，应该创建Indexer的引用，将对象Key传给它，就可以获取想要处理的对象
    - *NewIndexerInformer函数*
        - client-go中的基本控制器提供了 NewIndexerInformer 函数，用于创建Informer和Indexer。
        - 可以直接使用NewIndexerInformer 函数，或者也可以使用工厂方法来创建Informer
- **Resource Event Handlers**
    - 由具体的 Controller 给 Client-go 的Informer 提供的回调函数，获取待处理对象的key，并将key放入到Workqueue中。
- **Workqueue**
    - 此队列是 具体的Controller 内部创建的队列，用于暂时存储从Resource event handler 中 传递过来的，待处理对象的Key。
    - Resource event handler 函数通常会获取待处理对象的key，并将key放入到这个workqueue中。
- **Process Item**
    - 这个函数为循环函数，它不断从 Work queue 中取出对象的key，并使用 Indexer Reference 获取这个key对应的具体资源对象，然后根据资源的变化，做具体的调谐 Reconcile 动作。

### 特性

- discovery：用于发现API Server都是支持哪些API。kubectl apiversions使用了同样的机制
- dynamic：包含了kubernetes dynamic client的逻辑，可以操作任意的k8s资源API对象，包括内置的、自定义的资源对象
- informers：包含了所有内置资源的informer，便于操作k8s的资源对象
- kubernetes：包含了访问Kubernetes API的 所有ClientSet
- listers：包含了所有内置资源的lister，用于读取缓存中k8s资源对象的信息
- plugin/pkg/client/auth：包含所有可选的认证插件，用于从外部获取credential（凭证）
- tools：包含一系列工具，编写控制器时会用到很多里面的工具方法
- transport：包含了创建连接、认证的逻辑，会被上层的ClientSet使用

### 安装

```bash
go get k8s.io/client-go
```

### 文档

[Kubernetes API Reference Docs](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.24/#pod-v1-core)

## 附录

[https://github.com/kubernetes/client-go](https://github.com/kubernetes/client-go)