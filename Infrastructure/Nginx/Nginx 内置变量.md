---
title: Nginx 内置变量
date: 2026-04-07
tags: [基础设施, Nginx]
type: note
status: complete
---

# Nginx 内置变量

## 客户端相关

| 变量 | 含义 |
| --- | --- |
| `$remote_addr` | 客户端 IP 地址（直接连接 Nginx 的那个 IP） |
| `$remote_port` | 客户端端口号 |
| `$http_user_agent` | 客户端 UA（浏览器、curl 等） |
| `$http_x_forwarded_for` | 客户端请求头里的 `X-Forwarded-For`（如果有的话） |

## 服务器相关

| 变量 | 含义 |
| --- | --- |
| `$server_addr` | Nginx 服务器本地 IP（监听的网卡地址，需要 `--with-http_realip_module` 时自动获取） |
| `$server_name` | 匹配到的 server_name |
| `$server_port` | 服务器监听端口 |
| `$hostname` | 主机名（系统级） |

## 请求相关

| 变量 | 含义 |
| --- | --- |
| `$scheme` | 协议 (`http` 或 `https`) |
| `$request_method` | 请求方法（GET、POST 等） |
| `$request_uri` | 请求 URI（包含参数，如 `/index.php?foo=bar`） |
| `$uri` | 请求路径（不带参数） |
| `$args` | 请求参数（`foo=bar&x=1`） |
| `$query_string` | 与 `$args` 相同 |
| `$is_args` | 如果有参数则为 `?`，否则为空 |
| `$request_filename` | 请求对应的文件路径（根据 root/alias 拼接出来的） |

## 上游（proxy / fastcgi）相关

| 变量 | 含义 |
| --- | --- |
| `$proxy_add_x_forwarded_for` | `$http_x_forwarded_for, $remote_addr` 的组合，用来拼接代理链 |
| `$upstream_addr` | 上游服务器的 IP:PORT |
| `$upstream_status` | 上游返回的状态码 |
| `$upstream_response_time` | 上游响应时间 |
| `$upstream_connect_time` | 与上游建立连接耗时 |

## 请求时间相关

| 变量 | 含义 |
| --- | --- |
| `$time_iso8601` | 当前时间（ISO8601 格式） |
| `$time_local` | 当前时间（本地时间格式） |
| `$request_time` | 请求处理总耗时 |
| `$msec` | 毫秒时间戳 |

## 请求头相关

所有客户端请求头都可以通过 `$http_<header>` 获取，例如：

- `$http_host` → `Host`
- `$http_referer` → `Referer`
- `$http_cookie` → `Cookie`