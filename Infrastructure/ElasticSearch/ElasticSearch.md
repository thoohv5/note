---
title: ElasticSearch
date: 2026-04-07
tags:
  - 基础设施
  - ES
type: reference
status: complete
source: https://github.com/olivere/elastic
---

## ElasticSearch

Elasticsearch 是一个分布式、RESTful 的搜索和分析引擎，基于 Apache Lucene。

### 核心概念

| 概念 | 说明 | 类比 RDBS |
|------|------|-----------|
| Index | 文档集合 | Database |
| Type | 索引内逻辑分区（7.x 已废弃） | Table |
| Document | JSON 格式的基本数据单元 | Row |
| Field | 文档中的键值对 | Column |
| Shard | 索引分片，水平扩展 | Partition |
| Replica | 分片副本，高可用 | Replica |

### Go 客户端：olivere/elastic

```go
import "github.com/olivere/elastic/v7"

client, _ := elastic.NewClient(
    elastic.SetURL("http://localhost:9200"),
    elastic.SetSniff(false),
)
// 索引文档
client.Index().Index("posts").BodyJson(doc).Do(ctx)
// 搜索
result, _ := client.Search().Index("posts").Query(q).Do(ctx)
```

### 典型应用

- 全文搜索（日志、电商商品）
- 日志分析（ELK Stack: Elasticsearch + Logstash + Kibana）
- APM 性能监控
- 地理位置搜索

### 注意

- 内存需求较大，建议 ≥4GB。
- 避免深分页，使用 `search_after` 或 `scroll` API。
- 索引 mapping 创建后不可修改。

### 相关

- [[ElasticSearch查询]] [[Logstash]] [[Kibana]]