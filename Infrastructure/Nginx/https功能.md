---
title: https功能
date: 2026-04-07
  - 基础设施
  - Nginx
type: reference
status: complete
---

## https功能

## 概述

## 安装

### 模块检查

```bash
## 如果打印结果中有--with-http_ssl_module即已启用https功能
nginx -V
```

### 源码安装

```bash
./configure --prefix=/usr/local/nginx --with-http_ssl_module
make
make install
```

## 配置

```bash
server {
    # 默认端口为443
    listen 443 ssl;

    # 绑定证书上的的域名
    server_name xxx.xxx;

    # 证书文件
    ssl_certificate /doc/cert/server.crt;

    # 私钥文件
    ssl_certificate_key /doc/cert/server.key;

    location / {
        root   html;
        index  index.html index.htm;
    }
}
```

## 证书生成

### OpenSSL

```bash
#!/usr/bin/env bash

### root
#openssl genrsa -out ca.pem 2048
#openssl ecparam -genkey -name secp384r1 -out ca.pem
openssl genrsa -out ca.key 2048
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt -config conf/ca.cnf

### server
openssl genrsa -out server.key 2048
#openssl ecparam -genkey -name secp384r1 -out server.key
#openssl req -config conf/server.cnf -new -key server.key -out server_reqout.txt
#openssl x509 -req -in server_reqout.txt -days 3650 -sha1 -CAcreateserial -CA ca.crt -CAkey ca.pem -out server.crt -extfile conf/extfile.cnf
openssl req -new -key server.key -out server.csr -config conf/server.cnf
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 3650 -sha256 -extfile conf/extfile.cnf

### client
openssl genrsa -out client.key 2048
#openssl ecparam -genkey -name secp384r1 -out client.key
#openssl req -config conf/client.cnf -new -key client.key -out client_reqout.txt
#openssl x509 -req -in client_reqout.txt -days 3650 -sha1 -CAcreateserial -CA ca.crt -CAkey ca.pem -out client.crt
openssl req -new -key client.key -out client.csr -config conf/client.cnf
openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -days 3650 -sha256

```