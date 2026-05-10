---
title: RPM解压
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
---

## RPM解压

### 源码下载

下载分成两种方式，分成YUM源中有包或者自助下载

### YUM下载

```
## 下载package_name.rpm
yumdownloar [package]

## 下载package_name.src.rpm
yumdownloar --source [package]

```

### 自助下载

```
下载形如package_name.src.rpm的源码包

```

### 解压

```
## 依赖
yum install rpm2cpio

## 解压
rpm2cpio package_name.src.rpm | cpio -idmv

```

### 构建RPM打包目录

```
## 构建rpmbuild目录架构
mkdir -p ~/rpmbuild/{SOURCES,SPECS,BUILD,RPMS,SRPMS}

## 解压
rpm2cpio nmap-6.40-19.el7.src.rpm | cpio -idmv --quiet --no-absolute-filenames --preserve-modification-time --make-directories --pattern='*/*'

## 文件移动
mv * ~/rpmbuild/SOURCES/
find ~/rpmbuild/SOURCES/ -type f -name "*.spec" -exec mv {} ~/rpmbuild/SPECS/ \\;

## 文件授权
find ~/rpmbuild/SOURCES -type d -exec chmod 755 {} \\;
find ~/rpmbuild/SOURCES -type f -exec chmod 644 {} \\;

```