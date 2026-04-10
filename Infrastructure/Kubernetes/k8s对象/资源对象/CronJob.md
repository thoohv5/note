---
title: CronJob
date: 2026-04-07
tags: [基础设施, K8s]
type: reference
status: complete
---

# CronJob

# 概述

在 Kubernetes 中，**CronJob** 是 Job 的扩展，用于**按照指定时间计划（Cron 表达式）周期性地运行 Job**。它非常适合执行如定时备份、清理、发送通知、数据同步等周期性任务。

## 核心特性

| 特性 | 说明 |
| --- | --- |
| 📆 定时任务 | 按照 Cron 表达式运行 Job |
| 🔁 周期性 | 周期性运行，不需要手动触发 |
| ⏳ 并发控制 | 支持限制并发运行策略 |
| 💥 失败重试 | 可设置失败重试策略和历史保留数量 |
| 🧹 自动清理 | 可以配置保留成功/失败的 Job 数量 |

- 示例YAML
    
    ```yaml
    apiVersion: batch/v1
    kind: CronJob
    metadata:
      name: example-cronjob
    spec:
      schedule: "*/5 * * * *"    # 每 5 分钟执行一次
      successfulJobsHistoryLimit: 3  # 保留成功任务数
      failedJobsHistoryLimit: 1      # 保留失败任务数
      concurrencyPolicy: Forbid      # 禁止并发运行
      jobTemplate:
        spec:
          backoffLimit: 2
          template:
            spec:
              containers:
                - name: hello
                  image: busybox
                  args:
                    - /bin/sh
                    - -c
                    - date; echo Hello from CronJob
              restartPolicy: OnFailure
    ```
    

### schedule 字段 - Cron 表达式说明

格式：`分 时 日 月 星期`

- 例如：
    
    
    | 表达式 | 说明 |
    | --- | --- |
    | `* * * * *` | 每分钟 |
    | `*/5 * * * *` | 每 5 分钟 |
    | `0 0 * * *` | 每天午夜 00:00 |
    | `0 9 * * 1-5` | 工作日早上 9 点 |
    | `0 0 1 * *` | 每月 1 号 00:00 |

### 常用字段说明

| 字段 | 说明 |
| --- | --- |
| `schedule` | Cron 表达式，定义运行时间 |
| `concurrencyPolicy` | `Allow`（允许并发）、`Forbid`（禁止并发）、`Replace`（替换前一个） |
| `startingDeadlineSeconds` | 允许延迟启动的时间 |
| `successfulJobsHistoryLimit` | 保留成功 Job 的历史数量 |
| `failedJobsHistoryLimit` | 保留失败 Job 的历史数量 |

### 超时管理

在 K8s 中，主要通过以下三个参数进行精确控制：

**activeDeadlineSeconds (核心控制)**

这是控制 Pod 运行总时长的最直接方式。一旦超过该时间，K8s 会尝试终止该 Pod。

```yaml
spec:
  jobTemplate:
    spec:
      activeDeadlineSeconds: 300 # 如果任务运行超过 5 分钟，直接终止
      template:
        spec:
          containers:
          - name: worker
            image: my-job:latest
```

**concurrencyPolicy (并发策略)**

如果上一个任务还没跑完，下一个时间点又到了，该怎么办？

- **Allow** (默认): 允许多个任务同时运行。
- **Forbid**: 如果前一个没跑完，跳过当前任务（防止资源过载）。
- **Replace**: 如果前一个没跑完，杀掉旧的，启动新的。

**startingDeadlineSeconds (启动容错)**

如果因为调度问题（如资源不足）导致任务没能准时启动，这个参数定义了任务在错过计划时间后的**多少秒内**仍可被允许启动。如果超过这个时间还没启动，则记为失败。