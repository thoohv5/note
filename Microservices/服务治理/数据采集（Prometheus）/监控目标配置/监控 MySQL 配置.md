# 监控 MySQL 配置

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
#    environment:
#      - DATA_SOURCE_NAME=exporter:password@(mysql:3306)/mysql
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

- **ID:** `7362`
- **名字:** MySQL Overview
- **内容:** QPS、TPS、连接数、查询耗时、缓存命中率、慢查询等，非常细。