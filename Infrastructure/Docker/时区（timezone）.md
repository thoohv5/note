---
title: 时区（timezone）
date: 2026-04-07
tags:
  - 基础设施
  - Docker
type: note
status: incomplete
---

## 时区（timezone）

```bash
FROM alpine

RUN echo 'http://mirrors.ustc.edu.cn/alpine/v3.5/main' > /etc/apk/repositories \
&& echo 'http://mirrors.ustc.edu.cn/alpine/v3.5/community' >>/etc/apk/repositories \
&& apk update && apk add tzdata \
&& ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
&& echo "Asia/Shanghai" > /etc/timezone
```