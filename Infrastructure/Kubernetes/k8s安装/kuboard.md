---
title: kuboard
date: 2026-04-07
  - 基础设施
  - K8s
type: note
status: complete
---

## kuboard

```bash
sudo docker run -d 
--restart=unless-stopped \
--name=kuboard \
-p 6017:80/tcp \
-p 10081:10081/tcp \
-e KUBOARD_ENDPOINT="http://192.168.31.104:6017"  \
-e KUBOARD_AGENT_SERVER_TCP_PORT="10081" \
-e KUBOARD_DISABLE_OIDC=true \
-v /root/kuboard-data:/data \
eipwork/kuboard:v3
```

```bash
admin
Kuboard123
```

### **域名、反向代理配置**

1、域名配置参考：[https://www.cnblogs.com/lvlinguang/p/15195403.html](https://www.cnblogs.com/lvlinguang/p/15195403.html)

2、反向代理配置：在为 Kuboard 配置反向代理时，需要注意：

- WebContextRootKuboard 需要使用根路径进行访问，即 [https://kuboard.test.com/，而不能是https://kuboard.test.com/kuboard/](https://kuboard.test.com/，而不能)AFhttps://kuboard.test.com/kuboard/ 这样的二级路径；
- 启用 WebSocketKuboard 中终端和日志的功能需要用到WebSocket，需要在您的反向代理中配置WebSocket，才能正常使用 Kuboard 的终端和日志功能；

- Nginx 配置
    
    ```yaml
    http {
    
      # 您需要的其他配置
      map $http_upgrade $connection_upgrade {
          default upgrade;
          '' close;
      }
    
      server {
        listen       80;
        server_name  kuboard.test.com; # 替换成你的域名
    
        location / {
          proxy_pass http://192.168.31.104:6017/;  # 替换成你的 Kuboard IP 地址和端口，应该是 IP 地址，而不是 KUBOARD_ENDPOINT 参数的值
          client_max_body_size 10m;
          gzip on;
        }
    
        location /k8s-ws/ {
          proxy_pass  http://192.168.31.104:6017/k8s-ws/;  # 替换成你的 Kuboard IP 地址和端口
          proxy_http_version 1.1;
          proxy_pass_header Authorization;
          proxy_set_header Upgrade "websocket";
          proxy_set_header Connection "upgrade";
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          # proxy_set_header X-Forwarded-Proto https; # 如果您在反向代理上启用了 HTTPS
        }
    
        location /k8s-proxy/ {
          proxy_pass  http://192.168.31.104:6017/k8s-proxy/;  # 替换成你的 Kuboard IP 地址和端口
          proxy_http_version 1.1;
          proxy_pass_header Authorization;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection $connection_upgrade;
    
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          # proxy_set_header X-Forwarded-Proto https; # 如果您在反向代理上启用了 HTTPS
          gzip on;
        }
    
        error_page 404 /404.html;
            location = /40x.html {
        }
    
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
      }
    }
    ```
    

## 附录

[https://kuboard.cn/install/v3/install-built-in.html#部署计划](https://kuboard.cn/install/v3/install-built-in.html#部署计)92