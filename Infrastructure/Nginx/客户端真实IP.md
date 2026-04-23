---
title: 客户端真实IP
date: 2026-04-07
  - 基础设施
  - Nginx
type: note
status: complete
---

## 客户端真实IP

通过测试模拟这样的环境验证下：

192.168.118.14 - ngx ：该ngx 只是充当负载均衡，开启 X-Forwarded-For

192.168.118.15 - ngx：该ngx 为 web服务器，真正的用户端IP判断在这里实现

192.168.118.16 - httpd：充当 测试程序

配置如下：

192.168.118.14（模拟负载均衡）：

```yaml
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # 转发客户端真实
IPlocation / {    
	proxy_pass http://192.168.118.15/; # 仅仅用作转发    
	access_log logs/14_access.log main;    
	error_log logs/14_error.log;    
	#root   html;    
	#index  index.html index.htm;
}
```

192.168.118.15（nginx - 用户端IP判断在此实现）

```yaml
upstream back1 {    
	server 192.168.118.16:8080;
}

server {    
	listen       80;    
	server_name  localhost;    
	location / {        
		if ($http_x_forwarded_for ~* "192.168.118.2") {            
			rewrite ^/(.*)$ http://192.168.118.15/back1/$1 break;        
		}        
		root html;        
		index index.html;        
		access_log logs/15_access.log main;        
		error_log logs/15_error.log;    
	}    
	
	location /back1 {        
		proxy_pass http://back1/;    
	}
```

因为负载均衡可以转发用户真实IP，所以在 nginx 中，直接判断 http_x_forwarded_for 就能做路由。

192.168.118.16 开启 8080 端口

```yaml
## curl http://192.168.118.16:8080/a.html

<h1>192.168.118.16: a.html</h1>
```

直接访问 192.168.118.15

```yaml
## curl http://192.168.118.15/a.html

<h1>192.168.118.15: a.html</h1>
```

定义的规则：当客户端 192.168.118.2 访问 192.168.118.14时，则转发到 192.168.118.16:8080，剩下其他客户端IP则直接访问 192.168.118.15