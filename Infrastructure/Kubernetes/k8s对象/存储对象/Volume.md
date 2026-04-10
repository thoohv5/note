---
title: Volume
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: complete
---

# Volume

# 概述

`Volume`是`Pod`中能够被多个容器访问的共享目录。`Kubernetes`的`volume`概念、用途和目的与`Docker`的`Volume`比较类似，但两者不能等价。

**首先**，`Kubernetes`中的`volume`定义在`Pod`上，然后被一个`Pod`里的多个容器挂载到具体的文件目录下;**其次**，`Kubernetes`中的`volume`与`Pod`的生命周期相同，但与容器的生命周期不相关，当容器终止或者重启时，`volume`中的数据也不会丢失。**最后**，`Kubernetes`支持多种类型的`Volume`，例如GlusterFS、Ceph等先进的分布式文件系统。

**Volume 里的信息就是仿佛是被 Kubernetes“投射”（Project）进入容器当中的。**

- 示例YAML
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: two-containers
    spec:
      restartPolicy: Never
      volumes:
      - name: shared-data
        hostPath:      
          path: /data
      containers:
      - name: nginx-container
        image: nginx
        volumeMounts:
        - name: shared-data
          mountPath: /usr/share/nginx/html
      - name: debian-container
        image: debian
        volumeMounts:
        - name: shared-data
          mountPath: /pod-data
        command: ["/bin/sh"]
        args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]
    ```
    

## `Volume`类型

### `emptyDir`

一个`emptyDirVolume`是在`Pod`分配到`Node`时创建的。从它的名称就可以看出，它的初始内容为空，并且无须指定宿主机上对应的目录文件，因为这是`Kubernetes`白动分配的一个目录，当`Pod`从`Node`上移除时，`emptyDir`中的数据也会被永久删除。`emptyDir`的一些用途如下。

- 临时空间，例如用于某些应用程序运行时所需的临时目录，且无须永久保留。
- 长时间任务的中间过程`CheckPoint`的临时保存目录。
- 一个容器需要从另一个容器中获取数据的目录(多容器共享目录)。
- 示例YAML
    
    ```yaml
    volumes:
      -name: "temporary"
       storage: "emptyDir"
    ```
    

### `hostPath`

`hostPath`为在`Pod`上挂载宿主机上的文件或目录，它通常可以用于以下几方面。

- 容器应用程序生成的日志文件需要永久保存时，可以使用宿主机的高速文件系统进行存储。
- 需要访问宿主机上`Docker`引擎内部数据结构的容器应用时，可以通过定义`hostPath`为宿主机`/var/ib/docker`目录，使容器内部应用可以直接访问`Docker`的文件系统。
- 示例YAML
    
    ```yaml
    volumes:
      -name: "persistent"
       storage: "hostPath"
         path: "/data"
    ```
    

### [[Secret]]

### [[ConfigMap]]

### 其他类型的`Volume`

`gitRepo`:通过挂载 一个空目录，并从GIT库clone一个gitrepository以供Pod使用。

`NFS`使用NFS网络文件系统提供的共享目录存储数据时，我们需要在系统中部署一个NFSServer。

定义NFS类型的Volume的示例如下:

- 示例YAML
    
    ```yaml
    volumes:
      -name: "nfs"
       nfs:
         #改为你的NFS服务器地址
         server: nfs-sezver.local
         hostpath: "/"
    ```
    

# 附录