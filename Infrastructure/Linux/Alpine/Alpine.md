---
title: Alpine
date: 2026-04-07
  - 基础设施
  - Linux
type: guide
status: complete
---

## Alpine

```
Alpine Linux is an independent(独立的), non-commercial(非商业的), general purpose(通用的) Linux distribution designed for power users who appreciate security(安全), simplicity(简单) and resource efficiency(高效). Alpine Linux is built around musl libc and [busybox](<https://so.csdn.net/so/search?q=busybox&spm=1001.2101.3001.7020>).

```

### 配置国内镜像源

```
vim /etc/apk/repositories

<https://dl-cdn.alpinelinux.org/alpine/v3.14/main>
<https://dl-cdn.alpinelinux.org/alpine/v3.14/community>

```

备注：配置镜像源之后，需要[[Alpine#更新包索引]]

### 切换为阿里镜像源

```
sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

```

### 切换中科大镜像源

```
sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories

```

### 操作

### 更新包索引

```
apk update

```

### 搜索包

官网：[https://pkgs.alpinelinux.org/packages](https://pkgs.alpinelinux.org/packages)

```
apk search jdk

```

### 查询包信息

```
apk info -a openjdk17-jdk

```

### 升级包

```
apk upgrade

```

### 安装包

```
apk add openjdk17-jdk

```

### 移除包

```
apk del openjdk17-jdk

```

### 拓展

### Alpine安装telnet

```
apk add busybox-extras

```

## 附录

[https://mirrors.ustc.edu.cn/help/index.html](https://mirrors.ustc.edu.cn/help/index.html)