# VRRP(Virtual Router Redundancy Protocol）虚拟路由冗余协议

# 目的

IETF（Internet Engineering Task Force，因特网工程任务组）推出了**VRRP（Virtual Router Redundancy Protocol）虚拟路由冗余协议**，**来解决局域网主机访问外部网络的可靠性问题**。

VRRP是一种容错协议，它通过把几台路由设备联合组成一台虚拟的路由设备，并通过一定的机制来保证当主机的下一跳设备出现故障时，可以及时将业务切换到其它设备，从而保持通讯的连续性和可靠性。

使用VRRP的优势在于：既不需要改变组网情况，也不需要在主机上配置任何动态路由或者路由发现协议，就可以获得更高可靠性的缺省路由。

## **工作过程**

路由器使用VRRP 功能后，会根据优先级确定自己在备份组中的角色。优先级高的路由器成为Master 路由器，优先级低的成为Backup 路由器。**Master 拥有对外服务的虚拟IP，提供各种网络功能，并定期发送VRRP 报文，通知备份组内的其他设备自己工作正常；Backup 路由器只接收Master 发来的报文信息，用来监控Master 的运行状态。当Master 失效时，Backup 路由器进行选举，优先级高的Backup 将成为新的Master 。**

在抢占方式下，当Backup 路由器收到VRRP 报文后，会将自己的优先级与报文中的优先级进行比较。如果大于通告报文中的优先级，则成为Master 路由器；否则将保持Backup状态；

在非抢占方式下，只要Master 路由器没有出现故障，备份组中的路由器始终保持Master 或Backup 状态，Backup 路由器即使随后被配置了更高的优先级也不会成为Master 路由器；

如果Backup 路由器的定时器超时后仍未收到Master 路由器发送来的VRRP报文，则认为Master 路由器已经无法正常工作，此时Backup 路由器会认为自己是Master 路由器，并对外发送VRRP报文。备份组内的路由器根据优先级选举出Master 路由 器，承担报文的转发功能。

## 选举机制

VRRP路由器在运行过程中有三种状态

- Initialize状态，系统启动后进入Initialize，此状态下路由器不对VRRP报文做任何处理
- Master状态
- Backuo状态

### 优先级选举

1. VRRP组中IP拥有者。如果虚拟IP地址与VRRP组中的某台VRRP路由器IP地址相同，则此路由器为IP地址拥有者，这台路由器将被定位主路由器。
2. 比较优先级。如果没有IP地址拥有者，则比较路由器的优先级，优先级的范围是0~255，优先级大的作为主路由器
3. 比较IP地址。在没有Ip地址拥有者和优先级相同的情况下，IP地址大的作为主路由器。

<aside>
💡 基于优先级的主备切换是在抢占模式下才有效

</aside>

# 附录

[VRRP概述](https://www.cnblogs.com/yechuan/archive/2012/04/17/2453707.html)

[redis高可用：keepalived+redis主从部署_ganghuige的博客-CSDN博客_keepalived redis](https://blog.csdn.net/liuguanghui1988/article/details/77098143)

[非抢占模式下故障后优先级降低却不切换问题--使用Keepalived实现非抢占模式主备切换_TiiiiiiiME的博客-CSDN博客_keepalived不切换](https://blog.csdn.net/dengruijin/article/details/119710957)