---
title: Harbor
date: 2026-04-07
  - 基础设施
  - Harbor
type: reference
status: complete
---

## Harbor

## 概述

Docker容器应用的开发和运行离不开可靠的镜像管理，虽然Docker官方也提供了公共的镜像仓库，但是从安全和效率等方面考虑，部署私有环境内的Registry也是非常必要的。Harbor是由VMware公司开源的企业级的Docker Registry管理项目，它包括权限管理(RBAC)、LDAP、日志审核、管理界面、自我注册、镜像复制和中文支持等功能

 Harbor的所有服务组件都是在Docker中部署的，所以官方安装使用Docker-compose快速部署，所以需要[安装Docker](https://so.csdn.net/so/search?q=安)85Docker&spm=1001.2101.3001.7020、Docker-compose。由于Harbor是基于Docker Registry V2版本，所以就要求Docker-ce版本不小于17.06.0，Docker-compose版本大于等于1.6.0

## 安装

```bash
https://github.com/goharbor/harbor/releases

tar -zxvf harbor-offline-installer-v2.4.2.tgz #解压离线安装包
```

## 配置

```bash
cd harbor
cp harbor.yml.tmpl harbor.yml

vim harbor.yml

hostname = 192.168.31.100 		#修改harbor的启动ip为你虚拟机的ip
harbor_admin_password = Harbor12345 #修改harbor的admin用户的密码
```

## 启动

```bash
 bash install.sh
```

## 问题

```bash
Error response from daemon: Get "https://192.168.68.137/v2/": dial tcp 10.130.77.48:443: connect: no route to host

vim /usr/lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd --insecure-registry 192.168.68.137
修改配置文件，ExecStart之后添加 
 
 
重启docker 
    systemctl daemon-reload
    systemctl restart docker
```

```bash
ERROR:root:Error: The protocol is https but attribute ssl_cert is not set

修改配置文件，把https 注释掉即可：
```

```bash
## 进入 Harbor 的 PostgreSQL 容器
docker exec -it harbor-db psql -U postgres

## 切换到 harbor 数据库
\c registry

## 查看当前用户密码（可选）
SELECT * FROM harbor_user WHERE username='admin';

## 更新密码（新密码：NewHarbor12345）
UPDATE harbor_user SET password='$2a$10$9LZ5yZViXUQwKAZfQbYPD.3UK7DDA7JQ7XpXa1XvzW3JQO5Qe6v0W', salt='' WHERE username='admin';

## 退出
\q
exit
```