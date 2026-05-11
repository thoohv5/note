---
title: UPX
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
source: https://github.com/upx/upx
---

## UPX

> UPX (Ultimate Packer for eXecutables) 是一款开源的可执行文件压缩工具，支持多种平台格式。

### 安装

```bash
wget https://github.com/upx/upx/releases/download/v3.96/upx-3.96-amd64_linux.tar.xz
tar -Jxf upx*.tar.xz
cp upx*/upx /usr/bin
```

### 常用命令

```bash
# 压缩可执行文件
upx -9 binary

# 解压恢复
upx -d binary

# 查看信息
upx -l binary

# 测试（不解压运行）
upx -t binary
```

### 注意

- 压缩后文件体积可减少 30%-70%
- 运行时在内存中解压，启动时间略增
- 部分杀毒软件可能误报压缩后的可执行文件
- 适用于 ELF/PE/Mach-O 等格式

### 参考

- [UPX GitHub](https://github.com/upx/upx)