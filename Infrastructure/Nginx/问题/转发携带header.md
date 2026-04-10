# 转发携带header

```go
# 此处为微服务request_id传递参数配置
proxy_set_header x-request-id $request_id;
 
# 回显$request_id到浏览器response
add_header request_id $trace_id;
```