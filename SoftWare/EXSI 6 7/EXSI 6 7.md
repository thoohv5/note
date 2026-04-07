# EXSI 6.7

### 许可证

```bash
0A65P-00HD0-3Z5M1-M097M-22P7H
```

### 自动设置hostname

```bash
# 创建启动脚本
echo -e '#!/bin/sh\nesxcli system hostname set --host=workspace' > /etc/rc.local.d/local.sh
chmod +x /etc/rc.local.d/local.sh
```

# 附录

[[EXSI 6 7/ESXI 6 7 安装黑群晖7 1版本]]

[[EXSI 6 7/如何在#VMWare #ESXi 6 7上安装OpenWrt虚拟机充当旁路由]]