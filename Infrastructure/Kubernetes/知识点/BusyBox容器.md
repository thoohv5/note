---
title: BusyBox容器
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: complete
---

# BusyBox容器

# 概述

在日常的运维与开发工作中，我们时常需要一些轻量级工具来快速执行诊断命令、测试网络连接或验证服务状态。而`BusyBox`，正是这样一款功能强大且资源占用量极低的工具集，它集成了多种常用的Unix工具，如`sh`、`ls`、`ping`、`curl`等，非常适合在容器化环境中使用。

## 特点

- **轻量级**：BusyBox的镜像体积通常只有几MB，相比Ubuntu或CentOS等基础镜像，启动速度更快，资源占用更少。
- **功能丰富**：尽管体积小，BusyBox却集成了多种常用工具，能够满足大部分基本的运维和开发需求。
- **易于部署**：在Kubernetes等容器编排平台中，使用BusyBox镜像可以快速创建临时容器，用于执行诊断任务或进行测试。

### **使用场景**

- **网络诊断**：使用`ping`或`curl`命令测试网络连接，验证服务是否可达。
- **文件系统操作**：通过`ls`、`cat`等命令查看文件内容，检查配置文件是否正确。
- **Shell脚本执行**：在BusyBox容器中运行简单的Shell脚本，执行一系列诊断或测试任务。
- **临时调试**：当主应用容器出现问题时，可以快速启动一个BusyBox容器，进入相同的网络命名空间或挂载相同的卷，进行问题排查。

# 创建

### 命令行

```markdown
kubectl run busybox --image=busybox --restart=Never -- sleep 3600
```

### YAML文件

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"] 
```

```yaml
kubectl exec -it busybox-pod -- /bin/sh
```