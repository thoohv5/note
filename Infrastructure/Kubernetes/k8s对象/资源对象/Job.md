---
title: Job
date: 2026-04-07
tags:
  - 基础设施
  - K8s
type: reference
status: complete
---

## Job

## 概述

在 Kubernetes 中，**Job** 是一种控制器（Controller），用于**一次性运行的任务（批处理任务）**。它确保 Pod 按需成功地运行完成指定次数，适合执行短期任务、数据处理、数据库迁移、批量导入等操作。

### 核心特性

| 特性 | 说明 |
| --- | --- |
| ✅ 保证完成 | Job 会确保某个任务成功完成一定次数（成功一次或多次） |
| 🔁 重试机制 | 如果 Pod 执行失败，Job 会自动重试 |
| 🔚 非长期运行 | Job 适合有明确终止条件的任务 |
| 👨‍🔧 手动清理 | Job 默认不会自动删除，需手动或设置 TTL 清理策略 |

### 运行流程

- Job 控制器创建指定数量的 Pod。
- Pod 成功退出后，Job 标记为完成。
- 若 Pod 失败，Job 会重新创建新 Pod 直到完成或超过 `backoffLimit`。
- Job 可以设置 TTL 自动删除。

- 示例YAML
    
    ```yaml
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: example-job
    spec:
      completions: 1            # 要成功完成的次数（默认为 1）
      parallelism: 1            # 同时运行的 Pod 数量（并发执行）
      backoffLimit: 6           # 重试失败次数，超出失败（默认 6）
      ttlSecondsAfterFinished: 300  # 完成后 300 秒自动清理
      template:
        spec:
          containers:
            - name: job-example
              image: busybox
              command: ["sh", "-c", "echo Hello from Job && sleep 10"]
          restartPolicy: Never   # Job 的 Pod 通常设为 Never 或 OnFailure
    ```
    

## 清理

### **手动清理**

清理所有已完成的 Job

```bash
kubectl delete jobs --field-selector=status.successful=1
```

清理所有已完成的 Pod

```bash
kubectl delete pod --field-selector=status.phase==Succeeded
```

### **自动清理 Job（推荐）**

可以通过 Job 的 `ttlSecondsAfterFinished` 字段设置自动清理时间（单位：秒）：

### 示例 YAML

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: demo-job
spec:
  ttlSecondsAfterFinished: 300  # Job 完成 5 分钟后自动清理
  template:
    spec:
      containers:
        - name: demo
          image: busybox
          command: ["sh", "-c", "echo Hello; sleep 10"]
      restartPolicy: Never
```

<aside>
💡

`Kubernetes 1.21+` 才默认启用该字段。

</aside>