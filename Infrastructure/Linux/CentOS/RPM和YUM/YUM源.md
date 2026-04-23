---
title: YUM源
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: complete
---

## YUM源

## CentOS7

```bash
sudo yum install yum-utils -y

## 下载yum源
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
curl -o /etc/yum.repos.d/epel.repo http://mirrors.cloud.tencent.com/repo/epel-7.repo

## 清理&更新缓存
yum clean all && yum makecache
```

```yaml
cat > /etc/yum.repos.d/CentOS-Vault.repo << 'EOF'
[base]
name=CentOS-7 - Base
baseurl=https://vault.centos.org/7.9.2009/os/$basearch/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-7 - Updates
baseurl=https://vault.centos.org/7.9.2009/updates/$basearch/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-7 - Extras
baseurl=https://vault.centos.org/7.9.2009/extras/$basearch/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
EOF

sed -i '/^baseurl=/a sslverify=0' /etc/yum.repos.d/CentOS-Vault.repo

## 清理&更新缓存
yum clean all && yum makecache
```

### 证书过期

```yaml
yum install -y ca-certificates
update-ca-trust force-enable
update-ca-trust extract
```

## CentOS

```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-6.10.repo --no-check-certificate
```

## update & upgrade

```bash
yum check-update
```

yum -y update：升级所有包同时，也升级软件和系统内核；

yum -y upgrade：只升级所有包，不升级软件和系统内核，软件和内核保持原样。

唯一不同的是，yum upgrade会删除旧版本的package，而yum update则会保留(obsoletes=0)

## 附录

```yaml
yum install -y epel-release
yum -y install sshpass
```