# Centos安装K8S

## 集群基础

### 基础准备

[[网卡初始化]] 

```bash

###################网卡设置##########################

# 网卡编辑
vi /etc/sysconfig/network-scripts/ifcfg-ens33
BOOTPROTO="dhcp"          # 修改成BOOTPROTO="static"
ONBOOT="yes"              # 默认是yes，如果是no则改为"yes"

# 在配置文件的行尾添加一下配置
IPADDR=192.168.1.100     # 网卡的IP地址
GATEWAY=192.168.1.2     # 默认网关IP地址（虚拟机的默认网关都是xxx.xxx.xxx.2可以在虚拟网络编辑器里面查看不会的话百度）
NETMASK=255.255.255.0     # 子网掩码
DNS1=114.114.114.114      # dns1服务器
DNS2=8.8.8.8              # dns2服务器

:wq

# 重启网卡
systemctl restart network

###################网络设置##########################

# 在master服务器上设置主机名称
hostnamectl set-hostname master && bash

# 在node1服务器上设置主机名称
hostnamectl set-hostname node1 && bash

# 在node2服务器上设置主机名称
hostnamectl set-hostname node2 && bash

# 在master虚拟机添加hosts：
cat <<EOF >> /etc/hosts
192.168.1.100 master
192.168.1.101 node1
192.168.1.102 node2
EOF
# 在node101虚拟机添加hosts：
cat <<EOF >> /etc/hosts
127.0.0.1 node1
EOF
# 在node102虚拟机添加hosts：
cat >> /etc/hosts << EOF
127.0.0.1 node2
EOF

###################安全设置##########################

# 关闭防火墙
systemctl disable firewalld && systemctl stop firewalld

# 关闭selinux
sed -ri 's#(SELINUX=).*#\1disabled#' /etc/selinux/config

# 关闭swap分区
setenforce 0
swapoff -a
sed -i ' / swap / s/^\(.*\)$/#\1/g' /etc/fstab

###################系统设置##########################

# 修改系统的网络和内核参数
cat > /etc/sysctl.d/k8s.conf << EOF
# 将桥接的 IPv4 流量传递到 iptables 的链：
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward=1
vm.max_map_count=262144
EOF
modprobe br_netfilter
sysctl -p /etc/sysctl.d/k8s.conf

###################事件同步设置##########################

# 时间同步
yum install ntpdate -y
ntpdate ntp1.aliyun.com

###################软件设置（可选）##########################

# 安装依赖
sudo yum install -y device-mapper-persistent-data lvm2 vim wget lrzsz net-tools
```

# 集群安装

## 安装 Docker

[[../../Docker/知识点/Docker安装]] 

```bash
# 配置docker yum源
sudo yum install yum-utils -y
curl -o /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum clean all && yum makecache fast

# 修改系统的网络参数
cat <<EOF > /etc/sysctl.d/docker.conf
net.bridge.bridge-nf-call-ip6tables = 1	
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward=1
EOF
sysctl -p /etc/sysctl.d/docker.conf

# 安装docker-ce
# sudo yum install -y docker-ce-20.10.24-3.el7 docker-ce-cli-20.10.24-3.el7 containerd.io docker-compose-plugin
sudo yum install -y docker-ce-20.10.24-3.el7 docker-ce-cli-20.10.24-3.el7 containerd.io

# 启动& 开机启动docker； enable + start 二合一
systemctl enable docker --now

# 配置docker镜像加速以及使用 systemd 作为 cgroup 驱动
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
    "registry-mirrors": [
        "https://mirror.ccs.tencentyun.com",
        "https://docker.m.daocloud.io"
    ]
}
EOF

# 重新加载docker配置以及重启docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 安装 Kubernetes

```bash
# 添加kubernetes仓库
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# 安装kubeadm, kubelet, kubectl，版本都用1.23.6，1.24版本之后不支持docker了
yum install -y kubelet-1.23.6 kubeadm-1.23.6 kubectl-1.23.6

# 设置开机启动
systemctl enable kubelet
```

# 集群组建

### 初始化Master节点

```bash
# 初始化集群
kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.23.6 --pod-network-cidr=10.244.0.0/16

# 配置kubectl
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 配置kubectl

```bash
# master配置kubectl
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# node配置kubectl
mkdir -p $HOME/.kube
scp root@192.168.1.100:/etc/kubernetes/admin.conf /root/.kube/config
export KUBECONFIG=~/.kube/config

```

### 加入 Worker 节点

[[../知识点/命令行工具/kubeadm]] 

```bash
# 在 node1 和 node2 上执行 kubeadm join 命令，该命令在Master节点初始化集群的时候（kubeadm init） 成功后会输出。
kubeadm join <master-ip>:<master-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
# 例如
# kubeadm join 10.206.16.11:6443 --token nt0596.zfflhbum7d8k56ft --discovery-token-ca-cert-hash sha256:fc4adeab06c6759cf37372077ebf81913130646293cb1286e209988ba38688b9
```

### **验证集群状态**

```bash
# 查看集群状态是不是都是ready状态
kubectl get nodes
```

## 网络插件

### **Flannel网络插件**

```bash
# 依赖镜像
docker pull registry.cn-hangzhou.aliyuncs.com/liuk8s/flannel:v0.21.5
docker pull registry.cn-hangzhou.aliyuncs.com/liuk8s/flannel-cni-plugin:v1.1.2

# 安装
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 查看
kubectl get pods -n kube-flannel
```

### Calico网络插件

```bash
# 下载calico.yaml文件
curl http://docs.projectcalico.org/manifests/calico.yaml -O
wget https://docs.projectcalico.org/v3.25/manifests/calico.yaml

# 修改calico.yaml配置
            # The default IPv4 pool to create on startup if none exists. Pod IPs will be
            # chosen from this range. Changing this value after installation will have
            # no effect. This should fall within `--cluster-cidr`.
            # - name: CALICO_IPV4POOL_CIDR #将这段注释去掉
            #   value: "192.168.0.0/16"    #将这段注释去掉，并修改ip段为Master初始化集群的值，如--pod-network-cidr=192.168.0.0/16
            # Disable file logging so `kubectl logs` works.

# 查看calico.yaml镜像
grep image calico.yaml

# 将docker.io前缀替换
sed -i 's#docker.io/##g' calico.yaml

# 加载镜像，构建Calico网络插件
kubectl apply -f calico.yaml

# 最后查看网络插件是否都running
kubectl get po -n kube-system
```

## 其他

### **配置kubectl补全**

```bash
# 自动补全
yum -y install bash-completion

# 设置环境变量
echo "source /usr/share/bash-completion/bash_completion" >>  ~/.bashrc
echo 'source <(kubectl completion bash)' >>~/.bashrc
source ~/.bashrc

# 检查输出
type _init_completion
```

### 重置节点

```bash
# 重置节点
kubeadm reset --force
```

# 检查

集群状态

```bash
kubectl get nodes
```

组件状态

```bash
kubectl get componentstatuses
```

# 附录

[](https://zhuanlan.zhihu.com/p/1892690480699011878)

[kubeadm安装k8s集群超详细步骤2024-CSDN博客](https://blog.csdn.net/weixin_60185529/article/details/141728277)