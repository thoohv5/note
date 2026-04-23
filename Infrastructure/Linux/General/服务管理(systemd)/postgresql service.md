---
title: postgresql.service
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: incomplete
---

## postgresql.service

```bash
[Unit]
Description=Postgres Server
After=network.target

[Service]
Type=forking
User=postgres
Group=postgres
WorkingDirectory=/var/lib/postgres/data
ExecStart=/usr/local/postgres//pg_ctl start -D /var/lib/postgres/data
ExecReload=/usr/local/postgres//pg_ctl restart -D /var/lib/postgres/data
ExecStop=/usr/local/postgres//pg_ctl stop -D /var/lib/postgres/data
PrivateTmp=true

[Install]
WantedBy=multi-user.target

```