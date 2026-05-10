---
title: redis.service
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: incomplete
---

## redis.service

```bash
[Unit]
Description=Redis Server
After=network.target

[Service]
Type=forking
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
ExecReload=/usr/bin/redis-server -s reload
ExecStop=/usr/bin/redis-server -s stop
PrivateTmp=true

[Install]
WantedBy=multi-user.target

```