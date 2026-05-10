---
title: 告警配置（Alertmanager）
date: 2026-04-07
tags: [微服务, 服务治理, Prometheus]
type: note
status: complete
---

## 告警配置（Alertmanager）

### **安装**

```bash
docker run -d --name=alertmanager \
  -p 9093:9093 \
  -v /path/to/alertmanager.yml:/etc/alertmanager/alertmanager.yml \
  prom/alertmanager
```

### **配置告警规则**

在 **`prometheus.yml`** 中添加：

```yaml
rule_files:
  - alerts.yml
```

示例 **`alerts.yml`**：

```yaml
groups:
- name: example
  rules:
  - alert: HighCPU
    expr: node_cpu_seconds_total{mode="idle"} < 10
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "High CPU usage on {{ $labels.instance }}"
```

### **（3）配置告警通知**

在 **`alertmanager.yml`** 中设置（如邮件、Slack）：

```yaml
route:
  receiver: email

receivers:
- name: email
  email_configs:
  - to: admin@example.com
    from: alertmanager@example.com
    smtp_host: smtp.example.com
    smtp_port: 587
    auth_username: user
    auth_password: pass
```

---

## 附录

[Prometheus全面详解！！！（透透透）-CSDN博客](https://blog.csdn.net/m0_75233142/article/details/146704903)