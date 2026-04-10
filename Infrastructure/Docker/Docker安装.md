---
title: Docker安装
date: 2026-04-07
tags: [基础设施, Docker]
type: guide
status: complete
---

# Docker安装

# 依赖

## 源更新

```bash
sudo yum install -y yum-utils
# aliyun
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# curl -o /etc/yum.repos.d/docker-ce.repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum list docker-ce --showduplicates | sort -r
```

# 安装docker

### 版本20.10.9

```bash
# sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io docker-compose-plugin
sudo yum install docker-ce-20.10.9 docker-ce-cli-20.10.9 containerd.io docker-compose-plugin -y

# 启动& 开机启动docker； enable + start 二合一
systemctl enable docker --now
#sudo systemctl enable docker
#sudo systemctl start docker

# 测试demo
sudo docker run hello-world
```

### 配置docker镜像加速

```bash
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

### 源更新检查

```bash
# 检查
docker info | grep -A 3 "Registry Mirrors"

# 看看进程加载的配置
sudo ps aux | grep dockerd
```

# 安装docker-compose

### 版本1.27.4

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

docker-compose -v
```

### 版本2.24.5

```c
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

docker-compose -v
```