# NetworkManager

在CentOS系统上，目前有NetworkManager和network两种网络管理工具。如果两种都配置会引起冲突，而且NetworkManager在网络断开的时候，会清理路由，如果一些自定义的路由，没有加入到NetworkManager的配置文件中，路由就被清理掉，网络连接后需要自定义添加上去。

解决方法：
目前在CentOS上的NetworkManager版本比较低，而且比较适合有桌面环境的系统，所以服务器上保留network服务即可，将NetworkManager关闭，并且禁止开机启动。

```bash
systemctl stop NetworkManager

systemctl disable NetworkManager
```

# **系统设置了静态IP之后还会获取动态IP的问题解决**

```bash
root:/etc/sysconfig/network-scripts # cat ifcfg-ens34
BOOTPROTO="dhcp"
DEVICE="ens34"
ONBOOT="yes"
NM_CONTROLLED="no"  #加入此行，网卡就不受NetworkManager所控制
```

network：对网卡的配置
NetworkManager：这个服务由几个部分组成;一个是管理系统网络连接；一个是允许用户管理网络连接的客户端程序，使用它可以更好的管理网络