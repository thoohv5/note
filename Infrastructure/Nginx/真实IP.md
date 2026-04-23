---
title: 真实IP
date: 2026-04-07
  - 基础设施
  - Nginx
type: note
status: incomplete
---

## 真实IP

```bash
如果你想让上游知道真实客户端 IP → 用 X-Forwarded-For。

如果你只关心直接连接 Nginx 的那个 IP → 用 X-Real-IP。

如果你希望上游只看到 Nginx 自己的 IP（隐藏客户端）：

proxy_set_header X-Forwarded-For $server_addr;
proxy_set_header X-Real-IP $server_addr;
```