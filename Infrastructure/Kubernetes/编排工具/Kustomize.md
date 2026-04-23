---
title: Kustomize
date: 2026-04-07
  - 基础设施
  - K8s
type: note
status: complete
---

## Kustomize

最初，由 Kubernetes 官方给出的“如何封装应用”的解决方案是“用配置文件来配置文件”，这不是绕口令，你可以把它理解为是一种针对 YAML 的模版引擎的变体。

Kubernetes 官方认为，应用就是一组具有相同目标的 Kubernetes 资源的集合，如果逐一管理、部署每项资源元数据太麻烦啰嗦的话，那就提供一种便捷的方式，把应用中不变的信息与易变的信息分离开，以此解决管理问题；把应用所有涉及的资源自动生成一个多合一（All-in-One）的整合包，以此解决部署问题。这有点类似于springboot的自动装配的思想。

而完成这项工作的工具就叫做Kustomize，它原本只是一个独立的[小程序](https://cloud.tencent.com/product/tcb?from_column=20065&from=20065)，从 Kubernetes 1.14 起，被纳入了 kubectl 命令之中，成为随着 Kubernetes 提供的内置功能。Kustomize 使用Kustomization 文件来组织与应用相关的所有资源，Kustomization 本身也是一个以 YAML 格式编写的配置文件，里面定义了构成应用的全部资源，以及资源中需根据情况被覆盖的变量值。

Kustomize 的主要价值是根据环境来生成不同的部署配置。只要建立多个 Kustomization 文件，开发人员就能以基于基准进行派生（Base and Overlay）的方式，对不同的模式（比如生产模式、调试模式）、不同的项目（同一个产品对不同客户的客制化）定制出不同的资源整合包。

在配置文件里，无论是开发关心的信息，还是运维关心的信息，只要是在元数据中有描述的内容，最初都是由开发人员来编写的，然后在编译期间由负责 CI/CD 的产品人员针对项目进行定制。最后在部署期间，由运维人员通过 kubectl 的补丁（Patch）机制更改其中需要运维去关注的属性，比如构造一个补丁来增加 Deployment 的副本个数，构造另外一个补丁来设置 Pod 的内存限制，等等。

```yaml
k8s
 ├── base
 │     ├── deployment.yaml
 │     ├── kustomization.yaml
 │     └── service.yaml
 └── overlays
       └── prod
       │     ├── load-loadbalancer-service.yaml
       │     └── kustomization.yaml
       └── debug
             └── kustomization.yaml
```