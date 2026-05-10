---
title: centos
date: 2026-04-07
tags: [微服务, 分布式, 服务发现]
type: note
status: complete
---

### centos

```bash
### 官网：
https://github.com/etcd-io/etcd/tree/main/client/v3
https://pkg.go.dev/github.com/coreos/etcd/clientv3#pkg-index

### 安装依赖
go get go.etcd.io/etcd/client/v3

### 安装etcd
[root@node01 ~]# yum install -y etcd
### 设置开机自启动
systemctl enable etcd
### 启动etcd
systemctl start etcd
### 查看etcd运行状态
systemctl status etcd

### systemd配置
从systemctl status etcd命令的输出可以看到，etcd的 systemd配置文件位于/usr/lib/systemd/system/etcd.service，该配置文件的内容如下：

$ cat /usr/lib/systemd/system/etcd.service
[Unit]
Description=Etcd Server
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
WorkingDirectory=/var/lib/etcd/
EnvironmentFile=-/etc/etcd/etcd.conf
User=etcd
### set GOMAXPROCS to number of processors
ExecStart=/bin/bash -c "GOMAXPROCS=$(nproc) /usr/bin/etcd --name=\"${ETCD_NAME}\" --data-dir=\"${ETCD_DATA_DIR}\" --listen-client-urls=\"${ETCD_LISTEN_CLIENT_URLS}\""
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target

### 从上面的配置中可以看到，etcd的配置文件位于/etc/etcd/etcd.conf，如果我们想要修改某些配置项，可以编辑该文件。

### 远程访问
etcd安装完成后，默认只能本地访问，如果需要开启远程访问，还需要修改/etc/etcd/etcd.conf中的配置。例如，本实例中我安装etcd的机器IP是10.103.18.41，我尝试通过自己的机器远程访问10.103.18.41上安装的etcd的2379端口，结果访问被拒绝：

### 修改/etc/etcd/etcd.conf配置：
ETCD_LISTEN_CLIENT_URLS="http://10.103.18.41:2379,http://localhost:2379"

### 然后重启
systemctl restart etcd
```