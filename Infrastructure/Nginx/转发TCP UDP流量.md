---
title: 转发TCP/UDP流量
date: 2026-04-07
tags:
  - 基础设施
  - Nginx
type: note
status: complete
---

## 转发TCP/UDP流量

- `proxy_pass`
    - 设置代理服务器地址，可以为域名或IP。
    - 只能配置在server块中。
- `proxy_buffer_size`
    - 从上游服务器或客户端读取数据的缓冲区大小。默认大小16K:`proxy_buffer_size 16k;`
    - 可配置在`stream, server`块中。
    - 1.9.4版本开始引入的指令
- `proxy_connect_timeout`
    - 与代理服务器建立连接的超时时间。
    - 默认60秒，`proxy_connect_timeout 60s;`
    - 可配置在`stream, server`块中。
- `proxy_protocol`
    - 启用/关闭PROXY协议，启用后代理会将真实客户端连接信息传递给上游服务器。
    - 默认关闭。
    - 可配置在`stream, server`块中。
    - 语法：`proxy_protocol on | off;`
- `proxy_timeout`
    - 与上游服务器或客户端的连接有效时间，在此时间范围内没有数据传输连接会被关闭。默认值10m。
    - 可配置在`stream, server`块中。

## 示例

```yaml
#user  nobody;worker_processes  1;
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#pid        logs/nginx.pid;

events {    
	worker_connections  1024;
}
stream {        
	upstream oracle {           
		server 192.168.0.1:1521;   #原oracle地址    
	}
	
 upstream mysql {           
		server 192.168.1.102:3306;    
 }
 
 server {        
	 listen  3335;# 反向代理后监听的端口,nginx启动后访问192.168.21.100:3335就可以访问到oracleA        
	 proxy_connect_timeout 1s;        
	 proxy_timeout 3s;        
	 proxy_pass oracle;    
 }
 server {        
	 listen  8888;# 反向代理后监听的端口        
	 proxy_connect_timeout 1s;        
	 proxy_timeout 3s;        
	 proxy_pass mysql;    
 }
}
```