---
title: Nginx location
date: 2026-04-07
tags:
  - 基础设施
  - Nginx
type: note
status: complete
---

## Nginx location

**location如果没有“/”时，请求就可以模糊匹配以字符串开头的所有字符串，而有“/”时，只能精确匹配字符本身。**

```
server {
  listen 80;
  server_name localhost;

  # <http://localhost/wddd01/xxx> -> <http://localhost:8080/wddd01/xxx>
  location /wddd01/ {
    proxy_pass <http://localhost:8080>;
  }

  # <http://localhost/wddd02/xxx> -> <http://localhost:8080/xxx>
  location /wddd02/ {
    proxy_pass <http://localhost:8080/>;
  }

  # <http://localhost/wddd03/xxx> -> <http://localhost:8080/wddd03*/xxx>
  location /wddd03 {
    proxy_pass <http://localhost:8080>;
  }

  # <http://localhost/wddd04/xxx> -> <http://localhost:8080//xxx，请注意这里的双斜线，好好分析一下。>
  location /wddd04 {
    proxy_pass <http://localhost:8080/>;
  }

  # <http://localhost/wddd05/xxx> -> <http://localhost:8080/hahaxxx，请注意这里的haha和xxx之间没有斜杠，分析一下原因。>
  location /wddd05/ {
    proxy_pass <http://localhost:8080/haha>;
  }

  # <http://localhost/wddd06/xxx> -> <http://localhost:8080/haha/xxx>
  location /wddd06/ {
    proxy_pass <http://localhost:8080/haha/>;
  }

  # <http://localhost/wddd07/xxx> -> <http://localhost:8080/haha/xxx>
  location /wddd07 {
    proxy_pass <http://localhost:8080/haha>;
  }

  # <http://localhost/wddd08/xxx> -> <http://localhost:8080/haha//xxx，请注意这里的双斜杠。>
  location /wddd08 {
    proxy_pass <http://localhost:8080/haha/>;
  }
}

```

## 总结

```
proxy_pass代理地址端口后无任何字符，转发后地址：代理地址+访问URL目录部分
proxy_pass代理地址端口后有目录(包括 / )，转发后地址：代理地址+访问URL目录部分去除location匹配目录

```