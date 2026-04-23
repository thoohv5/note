---
title: 转发携带header
date: 2026-04-07
  - 基础设施
  - Nginx
type: note
status: incomplete
---

## 转发携带header

```go
## 此处为微服务request_id传递参数配置
proxy_set_header x-request-id $request_id;
 
## 回显$request_id到浏览器response
add_header request_id $trace_id;
```