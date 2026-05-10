---
title: upstream指令
date: 2026-04-07
tags:
  - 基础设施
  - Nginx
type: reference
status: complete
---

## upstream指令

## 概述

nginx中通过upstream实现负载均衡配置，upstream块内可以定义一组服务，nginx按不同的策略将请求转发到这些服务上实现负载均衡。

## 示例

```bash
upstream balance {
 server localhost:9090;
 server localhost:9091;
 server localhost:9092;
}

server {
 listen       8299;
 server_name  localhost;
 location / {
  proxy_pass http://balance; # balance为upstream后面配置的名称
 }
}
```

### upstream指令的参数

```bash
upstream balance {
 server localhost:9090 down;
 server localhost:9091 backup;
 server localhost:9092 max_conns=200 max_fails=3 fail_timeout=10;
}
```

- `max_conns`：限制最大同时连接数
- `down`：服务不可用，禁止访问此服务
- `backup`：备用机，只有在其他服务器无法访问的时候才能访问到，不适用于hash负载均衡、随机负载均衡
- `max_fails`：表示失败几次，则标记此服务不可用，默认值1
- `fail_timeout`：在fail_timeout设置的时间段内，请求失败max_fails次后标记此服务不可用。fail_timeout默认值10。

## 负载均衡策略

### 轮循

按先后顺序逐个转发到各个服务。

```bash
upstream balance { 
	server localhost:9090; 
	server localhost:9091; 
	server localhost:9092;
}
```

### 加权轮循

轮循upstream内的服务，按配置的权重比例分配请求。

```bash
upstream balance { 
	*# weight设置权重* server 
	localhost:9090 weight=1; 
	server localhost:9091 weight=5; 
	server localhost:9092 weight=2;
}
```

### ip哈希

根据客户端的ip地址计算出一个哈希值，根据哈希值不同将请求转发到不同的服务上，来自同一个ip的请求总是被分配到同一的服务上。

```bash
upstream balance { 
	ip_hash; 
	server localhost:9090; 
	server localhost:9091; 
	server localhost:9092;
}
```

### uri哈希

根据请求的uri计算哈希值，根据哈希值不同将请求转发到不同的服务上，相同uri的请求总是被分配到同一个服务上。

```bash
upstream balance { 
	hash $request_uri; 
	server localhost:9090; 
	server localhost:9091; 
	server localhost:9092;
}
```

### 最少连接

将请求分配到连接最少的服务上。

```bash
upstream balance { 
	least_conn; 
	server localhost:9090; 
	server localhost:9091; 
	server localhost:9092;
}
```

### 随机

将请求随机转发。

```
upstream balance {
 random;
 server localhost:9090;
 server localhost:9091;
 server localhost:9092;
}

```