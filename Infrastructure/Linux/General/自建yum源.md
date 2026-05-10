---
title: 自建yum源
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: guide
status: complete
---

## 自建yum源

## 服务端配置

### 创建文件夹

```
mkdir -p /data/repo/{x86_64,aarch64,ppc64}

```

### 软件依赖

```
yum -y install createrepo yum-utils

```

### 索引文件

```
createrepo -pdo /data/repo/x86_64 /data/repo/x86_64

```

### 包更新

### 下载不安装

```
yumdownloader openssl-*

```

### 自行制作，上传

```

```

### 更新

```
createrepo -update /data/repo/x86_64

```

### Web服务

```
python -m SimpleHTTPServer 8081 &>/dev/null &

```

## 客户端配置

### 配置

```
cd /etc/yum.repos.d

vi local.repo          # 连接本地 yum 源
repo 文件内容如下：
[local]
name=Server
baseurl=http://$LAN_IP
enable=1 # 是否开启，默认开启
gpgcheck=0 # 是否检查，0-不检查

```

### 开启&禁用

yum repolist

yum clean metadata dbcache

yum –-enablerepo=local –-disablerepo=base,extras,updates,repolist list available

### 清除缓存，并重建makecache

yum clean all && yum makecache