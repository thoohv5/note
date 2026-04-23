---
title: 控制组（Linux Control Groups，Linux CGroup）
date: 2026-04-07
  - 基础设施
  - Docker
type: reference
status: complete
---

## 控制组（Linux Control Groups，Linux CGroup）

## 概述

```
With control groups, CPU, disk, network, memory, and other system resources can be limited. We can create resource limits with cgroups.
```

**进程限制：**限制一个进程组能够使用的资源上限，包括 CPU、内存、磁盘、网络带宽等。

Cgroups 还能够对进程进行优先级设置、审计，以及将进程挂起和恢复等操作。

### **容器限制**

## Linux

```bash
sudo mkdir /sys/fs/cgroup/cpu/my_cgroup
sudo mkdir /sys/fs/cgroup/memory/my_cgroup

## cpu.cfs_period_us参数表示CPU周期的长度（以微秒为单位）
echo 100000 > /sys/fs/cgroup/cpu/my_cgroup/cpu.cfs_period_us
## cpu.cfs_quota_us参数表示在一个周期内分配给进程的CPU时间（以微秒为单位）
echo 50000 > /sys/fs/cgroup/cpu/my_cgroup/cpu.cfs_quota_us

## memory.limit_in_bytes参数表示分配给进程的最大内存量（以字节为单位）
echo 1073741824 > /sys/fs/cgroup/memory/my_cgroup/memory.limit_in_bytes

echo <pid> > /sys/fs/cgroup/cpu/my_cgroup/tasks
echo <pid> > /sys/fs/cgroup/memory/my_cgroup/tasks
```

### 示例

```bash
docker run --rm -d --cpus=2 --memory=2g --name=2c2g redis:alpine
cat /sys/fs/cgroup/system.slice/docker-`docker ps -lq --no-trunc`.scope/cpu.max
200000 100000
cat /sys/fs/cgroup/system.slice/docker-`docker ps -lq --no-trunc`.scope/memory.max
2147483648

docker run --rm -d  --cpus=0.5 --memory=0.5g --name=0.5c0.5g redis:alpine
cat /sys/fs/cgroup/system.slice/docker-`docker ps -lq --no-trunc`.scope/cpu.max
50000 100000
cat /sys/fs/cgroup/system.slice/docker-`docker ps -lq --no-trunc`.scope/memory.max
536870912
```