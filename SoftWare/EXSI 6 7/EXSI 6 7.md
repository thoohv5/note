---
title: EXSI 6.7
date: 2026-04-07
tags:
  - 软件工具
type: note
status: incomplete
---

## EXSI 6.7

### 许可证

```bash
0A65P-00HD0-3Z5M1-M097M-22P7H
```

### 自动设置hostname

```bash
## 创建启动脚本
echo -e '#!/bin/sh\nesxcli system hostname set --host=workspace' > /etc/rc.local.d/local.sh
chmod +x /etc/rc.local.d/local.sh
```

## 附录

[[SoftWare/EXSI 6 7/ESXI 6 7 安装黑群晖7 1版本|ESXI 6.7 安装黑群晖 7.1 版本]]

[[SoftWare/EXSI 6 7/VMWare ESXi 6 7 安装 OpenWrt 旁路由|VMWare ESXi 6.7 安装 OpenWrt 旁路由]]
