---
title: 监控 Redis 配置
date: 2026-04-07
tags: [微服务, 服务治理, Prometheus]
type: guide
status: complete
---

## 监控 Redis 配置

### 使用 `prom/mysqld-exporter` 监控MySQL：

```yaml
  exporter:
    image: prom/mysqld-exporter:latest
    container_name: exporter
    ports:
      - "9104:9104"
    volumes:
      - ./exporter/conf/my.cnf:/.my.cnf
    # 最新版本不支持了
##    environment:
##      - DATA_SOURCE_NAME=exporter:password@(mysql:3306)/mysql
    depends_on:
      - mysql
    networks:
      - ${NETWORK_NAME}
```

### 在 **`prometheus.yml`** 中添加：

```yaml
  - job_name: 'mysql'
    static_configs:
      - targets: ['192.168.31.130:9104']
        labels:
          instance: mysql
```

### Grafana模板

- **ID:** `11835`
- **名字:** Redis-Exporter Full
- **内容:** 内存使用率、命中率、key数量、连接数、慢查询、过期key等。