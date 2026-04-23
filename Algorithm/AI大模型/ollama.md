---
title: ollama
date: 2026-04-07
  - 算法
  - AI
type: note
status: complete
---

## ollama

## 安装

```yaml
curl -fsSL https://ollama.com/install.sh | sh
```

### 开启端口

```yaml
sudo vim /etc/systemd/system/ollama.service

[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="PATH=/root/.local/bin:/root/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin"

[Install]
WantedBy=default.target

sudo systemctl daemon-reload
```

```yaml
sudo firewall-cmd --add-port=11434/tcp --permanent
sudo firewall-cmd --reload
```