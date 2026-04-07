# url & body & header限制

# 概述

http协议对url长度没有限制
如果服务器不能处理太长的URI的时候，服务器应该返回414状态（此状态码代表Request-URI太长）

## GET 请求

### 浏览器

| 浏览器 | 最大长度（字符数） | 备注 |
| --- | --- | --- |
| Internet Explorer | 2083 | 如果超过这个数字，提交按钮没有任何反应 |
| Firefox | 65536 |  |
| Chrome | 8182 |  |
| Safari | 80000 |  |
| Opera | 190000 |  |
| curl | 8167 | linux环境 |

### 服务器

### nginx

```

# 客户端请求头信息最大缓冲区，默认：4K
client_header_buffer_size 512k;

# 客户端请求头信息的大小，默认：1K
large_client_header_buffers 7 512k;

```

## POST 请求

### 服务器

### nginx

```yaml
# 客户端请求体的消息，默认：1M
client_max_body_size 20m;
```