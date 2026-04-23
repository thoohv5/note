---
title: 数据可视化（Grafana）
date: 2026-04-07
  - 微服务
  - 服务治理
type: guide
status: incomplete
---

## 数据可视化（Grafana）

Prometheus 自带简单 UI，但推荐 **Grafana** 进行高级可视化：

### 安装

```bash
docker run -d --name=grafana \
  -p 3000:3000 \
  grafana/grafana
```

- **访问 Grafana**: **`http://<服务器IP>:3000`**（默认账号 **`admin/admin`**）
- **添加 Prometheus 数据源**：
    - URL: **`http://<Prometheus-IP>:9090`**
- **导入 Dashboard**：
    - Node Exporter 仪表盘 ID: **`1860`**
    - Docker 仪表盘 ID: **`193`**

---