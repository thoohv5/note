# IP操作

# 添加

### 临时IP

```bash

sudo ip addr add 192.168.1.100/24 dev eth0

sudo ifconfig eth0:1 192.168.1.100 netmask 255.255.255.0 up

# macos
sudo ifconfig en0 alias 192.168.65.151 192.168.65.255
```

### 永久IP

```bash
vi /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.243
NETMASK=255.255.255.0
DNS1=114.114.114.114
DNS2=8.8.8.8

:wq

systemctl restart network
```

# 查看

```bash
ip route show
```