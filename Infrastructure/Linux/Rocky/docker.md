---
title: docker
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: complete
---

## docker

### 1️⃣ 卸载旧版本（如果存在）

```bash
sudo dnf remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

---

### 2️⃣ 安装必要依赖

```bash
sudo dnf install -y dnf-plugins-core
```

---

### 3️⃣ 添加 Docker 官方仓库

```bash
------1---------
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

------2---------

## 备份原有配置
sudo cp /etc/yum.repos.d/docker-ce.repo /etc/yum.repos.d/docker-ce.repo.bak
## 删除错误配置
sudo rm -f /etc/yum.repos.d/docker-ce.repo

sudo tee /etc/yum.repos.d/docker-ce.repo <<-'EOF'
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/9/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
EOF

## 清理所有缓存
sudo yum clean all
## 重新生成缓存（关键：拉取新配置的仓库元数据）
sudo yum makecache
```

> 虽然是 centos repo，也适用于 Rocky Linux
> 

---

### 4️⃣ 安装 Docker Engine

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

> 注意：从 Docker 20.x 起，Docker Compose 作为 plugin 安装，不再是单独二进制包。
> 

---

### 5️⃣ 启动并设置开机自启

```bash
sudo systemctl enable --now docker
sudo systemctl status docker
```

---

### 6️⃣ 验证安装

```bash
docker version
docker compose version
```