---
title: Nginx中add_header和proxy_set_header的区别
date: 2026-04-07
  - 基础设施
  - Nginx
type: note
status: complete
---

## Nginx中add_header和proxy_set_header的区别

proxy_set_header是nginx设置请求头给上游服务器，add_header是nginx设置响应头信息给浏览器。

### proxy_set_header

```
语法格式：
proxy_set_header field value;
value值可以是包含文本、变量或者它们的组合。
常见的设置如：
proxy_set_header Host $proxy_host;
proxy_set_header version 1.0;

```

假如nginx请求上游服务器时，添加额外的请求头，就需要使用proxy_set_header。在java中使用HttpServletRequest.getHeader(String name)来获取请求头的值，name是请求头的名称。

### add_header

```
add_header 'Access-Control-Allow-Origin' '*';
add_header 'Access-Control-Allow-Headers' 'X-Requested-With';
add_header 'Access-Control-Allow-Methods' 'GET,POST,OPTIONS'
## 由于跨域请求，浏览器会先发送一个OPTIONS的预检请求，我们可以缓存第一次的预检请求的失效时间
if ($request_method = 'OPTIONS') {
	add_header 'Access-Control-Max-Age' 2592000;
	add_header 'Content-Type' 'text/plain; charset=utf-8';
	add_header 'Content-Length' 0;
	return 204;
}

```