---
title: 容器网络（Container Network）
date: 2026-04-07
tags:
  - 基础设施
  - Docker
type: note
status: complete
---

## 容器网络（Container Network）

## 容器网络技术栈

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/container_network_stack.jpg)

### 低层网络层（Low-level networking）

包括网络设备，iptables，路由，IPVLAN和Linux命名空间

### 容器网络层（Docker neworking）

Docker单节点网络

Docker多节点网络

CNI

### 容器编排层（Service discovery）

K8S网络

## Docker网络

Docker内建了3个网络

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_network_ls.png)

### `Docker single-host networking`（单节点网络**）**

### `Bridge`

**通常用于在独立容器中运行的应用程序，这是默认的Docker网络模型。**

Docker守护进程创建了docker0，它是一个虚拟以太网网桥，它可以在连接到它的任何port之间自动转发数据包。 默认情况下，守护进程会通过创建一对对的网络接口将主机上的所有容器连接到此网桥，将其中一个对等端分配为容器的eth0接口，并将另一个对等端放在主机的命名空间中；以及将专用IP范围内的一个子网分配给网桥。

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_bridge.png.jpg)

其特点如下：

- 使用一个 linux bridge，默认为 docker0
- 使用 veth 对，一头在容器的网络 namespace 中，一头在 docker0 上
- 该模式下Docker Container不具有一个公有IP，因为宿主机的IP地址与veth pair的 IP地址不在同一个网段内
- Docker采用 NAT 方式，将容器内部的服务监听的端口与宿主机的某一个端口port 进行"绑定"，使得宿主机以外的世界可以主动将网络报文发送至容器内部
- 外界访问容器内的服务时，需要访问宿主机的 IP 以及宿主机的端口 port
- NAT 模式由于是在三层网络上的实现手段，故肯定会影响网络的传输效率。
- 容器拥有独立、隔离的网络栈；让容器和宿主机以外的世界通过NAT建立通信
- 例：
    
    ![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_bridge_1.jpg)
    
    启动容器test1，自动获得一个IP 172.17.0.2，和主机docker0处于同一网络，且网关为docker0
    
    ![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_bridge_2.jpg)
    
    可以看到网桥docker0已经多了一个接口
    
    ![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_bridge_3.jpg)
    
    在主机上看到在POSTROUTING链中做了地址伪装，MASQUERADE动作，这样容器就可以通过源地址转换NAT访问外部网络了
    
    如果容器服务监听端口并使用-P做了映射，在主机还可以看到DNAT动作，这样外部用户就可以通过主机的映射端口访问容器内部服务
    
    `docker network inspect bridge`命令可以查看bridge网络详细情况
    

### `Host`

 **用于独立容器，共享主机的网络**。

该模式有效地禁用了Docker容器的网络隔离特性。容器有效地继承了主机的IP地址。这种模式比桥接模式更快，因为没有路由开销，但它将容器直接暴露给公共网络，并会带来所有安全隐患。此外作为共享主机网络命名空间的后果，您需要以某种方式管理端口的分配。

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_host.jpg)

其特点包括：

- 这种模式下的容器没有隔离的 network namespace
- 容器的 IP 地址同 Docker host 的 IP 地址
- 需要注意容器中服务的端口号不能与 Docker host 上已经使用的端口号相冲突
- host 模式能够和其它模式共存
- 例：
    
    ![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_host_1.jpg)
    
    启动容器test2，使用—net host指定为host网络类型，可以看到容器的IP与主机一样，容器服务监听了的80端口实际上就是在主机上监听的，外部访问容器直接使用主机IP即可，不需要NAT转换。但容器的其他信息（Pid、文件系统）等还是与主机是隔离的
    
    `docker network inspect host`命令可以查看host网络详细情况
    

### `Container`

 **共享其他容器网络**

容器会共享其他容器的网络环境，两个容器之间不存在网络隔离，而这两个容器又与宿主机以及除此之外其他的容器存在网络隔离，因此需要注意端口冲突情况，否则第二个容器将无法被启动。 Kubernetes网络使用这种模式。

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_container.jpg)

其特点是：

- 共享同一个容器网络的所有容器的网络信息是一致，除了网络信息外，其他均继续保持隔离
- 例:
    
    ![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_container_1.png)
    
    启动容器test3，使用--network container:test1指定使用已经存在的test1容器的网络，可以看到test3容器的网络信息与test1一样，是共享的
    

### `None`

 禁用Docker端的网络支持，并允许自定义网络。

不为 Docker 容器构造任何网络环境。一旦Docker 容器采用了none 网络模式，那么容器内部就只能使用loopback网络设备，不会再有其他的网络资源，容器只能使用127.0.0.1的本机网络。

- 例
    
    ![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_none_1.jpg)
    
    启动容器test4，使用--network none放弃为容器配置网络，进入容器查看除了lo外，没有其他网络设备，容器只能使用127.0.0.1的本地网络
    
    `docker network inspect none`命令可以查看none网络详细情况
    

### 自定义网络

### Bridge

```bash
## 创建自定义网络，其中--driver后面支持的类型有三种：bridge、macvlan、overlay
docker network create --driver network_type network_name

## 查看网络信息
docker network inspect network_name

## 查看网桥
brctl show
```

在主机上可以看到两个网桥对应的路由信息

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_bridge_4.jpg)

创建容器，通过`--network my-bridge`加入这个网络，同处于my-bridge的容器间网络是互通的，网络信息与默认的bridge没有差别（依然SNAT出网和DNAT入网），但该网桥与其他网桥之间是不通的。这种方式适用于单节点

### Overlay

Overlay 可以使得我们将报文在 IP 报文之上再次封装，VXLAN 技术为其核心，VXLAN是将以太网报文封装成UDP报文进行隧道传输，UDP目的端口为已知端口，源端口可按流分配，标准5元组方式有利于在IP网络转发过程中进行负载分担；隔离标识采用24比特来表示；未知目的、广播、组播等网络流量均被封装为组播转发。

> 单机模式是无法创建overlay网络模型的，需要借助分部署存储（etcd或者编排系统），部署完etcd后需要配置docker指定etcd地址后重启docker。
> 

```bash
## 创建overlay 网络（只需在一个节点执行）
docker network create -d overlay my-overlay
```

在两个主机分别创建容器，使用--network my-overlay加入overlay网络，可以看到容器有两块网卡，eth1为走普通NAT模式，eth0 是 overlay 网段上分配的IP地址，也就是它走的是 overlay 网络，它的 MTU 是 1450 而不是 1500。

docker 会为每个 overlay 网络创建一个独立的 network namespace，其中会有一个 linux bridge br0， veth pair 一端连接到容器中（即 eth0），另一端连接到 namespace 的 br0 上。br0 除了连接所有的 veth pair，还会连接一个 vxlan 设备，用于与其他 host 建立 vxlan tunnel。容器之间的数据就是通过这个 tunnel 通信的。

```bash
ln -s /var/run/docker/netns/ /var/run/netns
ip netns
```

可以看到两个 host 上有一个相同的 namespace

`ip netns exec 1-625e8bbfff brctl show`查看 namespace 中的 br0 上的设备

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/docker_overlay.jpg)

不同的 overlay 网络是相互隔离的。

docker 默认为 overlay 网络分配 24 位掩码的子网（10.0.0.0/24），所有主机共享这个 subnet，容器启动时会顺序从此空间分配 IP。当然我们也可以通过 --subnet 指定 IP 空间。

### `Docker multi-host networking`多节点网络

### **基于实现方式分为**

**隧道**

- `Weave`：UDP广播，本机建立新的BR，通过PCAP互通。
- `Open` vSwitch(OVS)：基于VxLAN和GRE协议，但是性能方面损失比较严重。
- `Flannel`：UDP广播，VxLan。

隧道方案在IaaS层的网络中应用也比较多，大家共识是随着节点规模的增长复杂度会提升，而且出了网络问题跟踪起来比较麻烦，大规模集群情况下这是需要考虑的一个点。

**路由**

- `Calico`：基于BGP协议的路由方案，支持很细致的ACL控制，对混合云亲和度比较高。
- `Macvlan`：从逻辑和Kernel层来看隔离性和性能最优的方案，基于二层隔离，所以需要二层路由器支持，大多数云服务商不支持，所以混合云上比较难以实现。

### 基于网络模型分为

`Docker Libnetwork Container Network Model(CNM)`阵营

- Docker Swarm overlay
- Macvlan & IP network drivers
- Calico
- Contiv（from Cisco）

Docker Libnetwork的优势就是原生，而且和Docker容器生命周期结合紧密；缺点也可以理解为是原生，被Docker"绑架"

`Container Network Interface(CNI)`阵营

- Kubernetes
- Weave
- Macvlan
- Flannel
- Calico
- Contiv
- Mesos CNI

兼容其他容器技术(e.g. rkt)及上层编排系统(Kuberneres & Mesos)，而且社区活跃势头迅猛，Kubernetes加上CoreOS主推；缺点是非Docker原生

## K8S网络

### Pod网络与Docker网络

![](https://github.com/zdnscloud/k8s-knowledge-share/raw/9c00633481d15b79e485ea75f002ea7db788178a/network/pictures/k8s-docker.png)

K8S中每个Pod在创建时都是先创建一个pause容器，业务容器与pause容器使用同一个网络名称空间。

`docker inspect $ID` 会发现业务容器的网络模式是container，而pause容器的网络模式是none，因为k8s使用了cni，具体的容器网卡、IP、路由等信息是有cni配置的

### Kubelet逻辑分析

kubelet调用createPodSandbox来创建pause容器，并为其配置网络环境

[github.com/kubernetes/pkg/kubelet/kuberuntime/kuberuntime_sandbox.go](http://github.com/kubernetes/pkg/kubelet/kuberuntime/kuberuntime_sandbox.go)

```go
func (m *kubeGenericRuntimeManager) createPodSandbox(pod *v1.Pod, attempt uint32) (string, string, error) {

      podSandBoxID, err := m.runtimeService.RunPodSandbox(podSandboxConfig, runtimeHandler)

}
```

[github.com/kubernetes/pkg/kubelet/dockershim/docker_sandbox.go](http://github.com/kubernetes/pkg/kubelet/dockershim/docker_sandbox.go)

```go
func (ds *dockerService) RunPodSandbox(ctx context.Context, r *runtimeapi.RunPodSandboxRequest) (*runtimeapi.RunPodSandboxResponse, error) {

      err = ds.client.StartContainer(createResp.ID)

     #DNS配置：通过docker inspecft查看容器信息，然后再重写容器位于宿主机的resolv文件

      err = ds.network.SetUpPod(config.GetMetadata().Namespace, config.GetMetadata().Name, cID, config.Annotations, networkOptions)

}
```

[github.com/kubernetes](http://github.com/kubernetes) /pkg/kubelet/dockershim/network/plugins.go

```go
func (pm *PluginManager) SetUpPod(podNamespace, podName string, id kubecontainer.ContainerID, annotations, options map[string]string) error {

      if err := pm.plugin.SetUpPod(podNamespace, podName, id, annotations, options); err != nil {

}
#调用plugin的SetUpPod方法，这里plugin是一个interface, 具体使用哪个plugin是由kubelet的启动参数–network-plugin决定的，我们配置的是cni

}
```

[github.com/kubernetes/pkg/kubelet/dockershim/network/cni/cni.go](http://github.com/kubernetes/pkg/kubelet/dockershim/network/cni/cni.go)

```go
## 获取配置文件
func getDefaultCNINetwork(confDir string, binDirs []string) (*cniNetwork, error) {

      files, err := libcni.ConfFiles(confDir, []string{.conf, .conflist, .json})

      switch {

      case err != nil:

              return nil, err

      case len(files) == 0:

              return nil, fmt.Errorf(No networks found in %s, confDir)

      }

      sort.Strings(files)

}

## 配置pause容器的eth0接口的网络
func (plugin *cniNetworkPlugin) SetUpPod(namespace string, name string, id kubecontainer.ContainerID, annotations, options map[string]string) error {

      _, err = plugin.addToNetwork(plugin.getDefaultNetwork(), name, namespace, id, netnsPath, annotations, options)

}

func (plugin *cniNetworkPlugin) addToNetwork(network *cniNetwork, podName string, podNamespace string, podSandboxID kubecontainer.ContainerID, podNetnsPath string, annotations, options map[string]string) (cnitypes.Result, error) {

      res, err := cniNet.AddNetworkList(netConf, rt)

}

func (c *CNIConfig) AddNetworkList(list *NetworkConfigList, rt *RuntimeConf) (types.Result, error) {

      prevResult, err = invoke.ExecPluginWithResult(pluginPath, newConf.Bytes, c.args(ADD, rt))

}
## 该函数会遍历plugin，根据cni的type在binDir中找到同名插件，返回该插件的全路径。最后执行ExecPluginWithResult函数，它将调用cni的二进制文件并传入newConf参数以及RuntimeConf和一个ADD参数，其中ADD代表给容器添加网络。

```

分析到这，kubelet的网络配置已经完成了，我们最终会看到kubelet在生成新pod的时候会先生成一个sandbox容器（pause），kubelet会根据pod的yaml信息和kubelet的cni参数配置生成一个cni runtime配置，最后调用cni插件完成docker容器的网络配置。