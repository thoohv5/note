---
title: Dockerfile
date: 2026-04-07
tags: [基础设施, Linux, Alpine]
type: note
status: complete
---

## Dockerfile

```bash
FROM golang:1.17-alpine

RUN set -xe; \
    # 更新镜像
    sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories \
    # 更新
    && apk update \
    # ca
    && apk add ca-certificates \
    # add make gcc g++ git openssl wget
    && apk --no-cache add make gcc musl-dev git openssl wget  \
    # 设置Docker 时间为上海时区
    && apk add -U tzdata && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo "Asia/shanghai" >> /etc/timezone \
    # 安装upx
    && wget https://github.com/upx/upx/releases/download/v3.96/upx-3.96-amd64_linux.tar.xz && tar -Jxf upx*.tar.xz && mv upx*/upx /usr/bin && rm -rf upx* \
```