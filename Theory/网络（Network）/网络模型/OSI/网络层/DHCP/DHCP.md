# DHCP

# 介绍

提供DHCP服务的软件

CentOS中提供DHCP服务的软件叫做dhcp，CentOS中默认没有安装。我们可以通过yum命令安装

`yum -y install dhcp`

## 配置文件

/etc/dhcp/dhcp.conf

/usr/share/doc/dhcp*/dhcpd.conf.sample

/var/lib/dhcpd/dhcpd.leases

### 配置文件介绍

```bash
# DNS默认搜索域名 /etc/resolv.conf >> search localhost.localdomain
option domain-name "localhost.localdomain";
# DNS域名服务器，多个DNS服务器使用逗号隔开 /etc/resolv.conf >> nameserver localhost.localdomain
option domain-name-servers "114.114.114.114";

# 默认租约时间，单位为秒
default-lease-time 600;
# 最大租约时间，单位为秒
max-lease-time 7200;
# DNS服务动态更新的类型，类型包括：none (不支持动态 更新)，interim (互动更新模式）与ad-hoc (特殊更新模式），默认为none
ddns-update-style none;
# 固定格式，允许/忽略客户端的DNS更新，和ddns-update-style搭配
allow/ignore client-updates;
# 设置路由器的IP
option routers "192.168.1.1";

# 地址池
subnet <NETWORK> netmask <子网掩码> {
	# option routers <路由IP>
	option routers 192.168.1.254;
	# option broadcast-address <广播IP>
	option broadcast-address 192.168.1.255;
	# option subnet-mask <子网掩码>
	option subnet-mask 255.255.255.0;
  # option domain-search <DNS搜索域名>
	option domain-search "example.com";
	# option domain-name-servers <DNS>
	option domain-name-servers 192.168.1.1;
  # 定义客户机的网络时间服务器（NTP)
  ntp-server IP <地址>
  # 定义客户机的NIS域服务器的地址
  nis-servers IP <地址>
	# option time-offset <时间，秒>
	option time-offset -18000;     # Eastern Standard Time
	# range <起始地址> <结束地址>
	range 192.168.1.10 192.168.1.100;
	# default-lease-time <默认租约时间，秒>
	default-lease-time 600;
  # default-lease-time <最大租约时间，秒>
	max-lease-time 7200;
} 
    
# 固定地址
host <xxx> {
	# option host-name <主机名>
	option host-name "apex.example.com";
	# hardware ethernet <MAC地址>
  hardware ethernet 00:A0:78:8E:9E:AA;
	# fixed-address <IP地址>
  fixed-address **192.168.1.4**;
}       

# 超级作用域
shared-network <xxx> {
    option domain-search "test.redhat.com";
    option domain-name-servers ns1.redhat.com, ns2.redhat.com;
    option routers 192.168.0.254;
    #more parameters for EXAMPLE shared-network
    subnet 192.168.1.0 netmask 255.255.252.0 {
        #parameters for subnet
        range 192.168.1.1 192.168.1.254;
    }
    subnet 192.168.2.0 netmask 255.255.252.0 {
        #parameters for subnet
        range 192.168.2.1 192.168.2.254;
    }
}

# 分组
group {
   option routers 192.168.1.254;
   option subnet-mask 255.255.255.0;
   option domain-search "example.com";
   option domain-name-servers 192.168.1.1;
   option time-offset -18000;     # Eastern Standard Time
   host apex {
      option host-name "apex.example.com";
      hardware ethernet 00:A0:78:8E:9E:AA;
      fixed-address 192.168.1.4;
   }
   host raleigh {
      option host-name "raleigh.example.com";
      hardware ethernet 00:A1:DD:74:C3:F2;
      fixed-address 192.168.1.6;
   }
}   
```

```yaml
# 网卡
dhcp-client-identifier

# 设备
vendor-class-identifier
```

```yaml
# 验证语法
dhcpd -t -cf /usr/local/appdata/normal/data/zddi/dhcp/etc/dhcpd.conf

ps -ef|grep [d]hcpd|awk '{print $2}'|xargs kill -9 && /usr/local/sbin/dhcpd -4 -q -cf /usr/local/appdata/normal/data/zddi/dhcp/etc/dhcpd.conf -lf /usr/local/appdata/normal/data/zddi/dhcp/db/dhcpd.leases &
```

# 附录

[CentOS 架设DHCP服务_比小白大一点的大白的技术博客_51CTO博客](https://blog.51cto.com/u_13570193/2103356)