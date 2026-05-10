---
title: Consul
date: 2026-04-07
tags: [微服务, 分布式, 服务发现]
type: note
status: complete
---

### Consul

```go
docker run -d -p 8500:8500 --name=consul \
consul:1.6.1 \
consul agent -dev \
-ui -node=n1 -bootstrap-expect=1 -client=0.0.0.0
```

consul agent -dev 使用开发模式启动

-ui 开启网页[可视化](https://so.csdn.net/so/search?q=可视)96&spm=1001.2101.3001.7020管理界面

-node 指定该节点名称，注意每个节点的名称必须唯一不能重复！

-bootstrap-expect 最少集群的Server节点数量，少于这个值则集群失效，这个选项必须指定，由于这里是单机部署，因此设定为1即可

-client 指定可以外部连接的地址，0.0.0.0表示外网全部可以连接