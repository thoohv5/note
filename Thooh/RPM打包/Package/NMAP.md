---
title: NMAP
date: 2023-12-15
tags:
  - 笔记
  - RPM打包
  - Package
type: note
status: incomplete
---


### kylin_arm

```
rpmbuild -ba nmap-7.93-1.spec --define "buildncat 0" --nodebuginfo
```

### kylin_x86/x64_86

```
rpmbuild -ba nmap-7.93-1.spec --define "buildncat 0" --nodebuginfo
```