---
title: 监控 Linux 服务器
date: 2026-04-07
  - 微服务
  - 服务治理
type: guide
status: complete
---

## 监控 Linux 服务器

### 使用 **Node Exporter** 收集主机指标：

```bash
docker run -d --name=node_exporter \
  -p 9100:9100 \
  -v "/:/host:ro,rslave" \
  quay.io/prometheus/node-exporter
```

### 在 **`prometheus.yml`** 中添加：

```yaml
scrape_configs:
  - job_name: "node"
    static_configs:
      - targets: ["<服务器IP>:9100"]
```

### Grafana 模板

- **ID:** `1860`
- **名字:** Node Exporter Full
- **内容:** CPU、内存、磁盘、网络，啥都有，服务器必备监控模板。