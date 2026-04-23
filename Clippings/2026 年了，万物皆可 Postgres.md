---
title: "2026 年了，万物皆可 Postgres"
source: "https://mp.weixin.qq.com/s/f41BzHMxbHnCKZc0ua4oRA"
author:
  - "[[Tiger Data]]"
published:
created: 2026-04-15
description: "把你的数据库想象成你的家。客厅、卧室、浴室、厨房、车库——各司其职，但都在同一个屋檐下，有门有走廊互相连通。"
tags:
  - "clippings"
---
原创 Tiger Data *2026年2月23日 09:52*

把你的数据库想象成你的家。客厅、卧室、浴室、厨房、车库——各司其职，但都在同一个屋檐下，有门有走廊互相连通。你不会为了做顿饭专门盖一栋餐厅，也不会为了停车就在城市另一头建个商业车库。

**这就是 Postgres。** 一栋房子，多个房间。搜索、向量、时序数据、消息队列——全在一个屋顶下。

但这恰恰是专用数据库厂商最不想让你听到的。他们的市场团队花了数年给你洗脑，劝你 *"为特定场景选择专用工具"* 。听起来很专业，很明智，顺便还能多卖几套数据库。

让我告诉你这为什么是陷阱，以及为什么 Postgres 在 99% 的场景下都是更优解。

### "选对工具"是个伪命题

你一定听过这句话："为正确的工作选择正确的工具"。

乍一听很有道理。于是你的技术栈变成了：

1. **Elasticsearch** 负责搜索
2. **Pinecone** 负责向量检索
3. **Redis** 负责缓存
4. **MongoDB** 负责文档存储
5. **Kafka** 负责消息队列
6. **InfluxDB** 负责时序数据
7. **PostgreSQL** 负责……兜底

恭喜你，现在你有七个数据库要维护。七种查询语言要学。七套备份策略要制定。七套安全模型要审计。六组凭证要轮换。七个监控面板要盯。还有七样东西可能在凌晨三点一起炸锅。

真要出问题了？祝你搭建测试环境排查顺利。

换个思路： **直接上 Postgres。**

### AI 时代的刚需：化繁为简

这不仅关乎简洁。 **AI Agent 的崛起让多数据库架构变成了噩梦。**

想想 Agent 的日常操作：

- 快速启动一个带生产数据的测试环境
- 尝试某个修复或实验
- 验证效果
- 销毁环境

单数据库架构？一条命令搞定。Fork，测试，完事。

七个数据库呢？你需要：

- 协调 Postgres、Elasticsearch、Pinecone、Redis、MongoDB 和 Kafka 的快照
- 确保时间戳对齐
- 启动七个服务
- 配置七条连接串
- 祈祷测试期间数据别漂移
- 测完再销毁七个服务

没有专门的基建团队，这几乎不可能。

还不只是 Agent 的问题。每次凌晨三点出故障，你都得搭测试环境排查。六个数据库的协调简直是地狱模式。单数据库？一条命令的事。

在 AI 时代， **简洁不是优雅，而是刚需。**

### "但专用数据库性能更强啊！"

我们来直面这个问题。

**迷思：** 专用数据库在各自领域碾压通用方案。

**现实：** 某些极端场景下它们确实有优势，但代价是不必要的复杂度。就像为了每顿饭请一位私人厨师——听起来很奢华，但增加了开销、协调成本，还制造了原本不存在的问题。

关键是： **99% 的公司根本用不上它们。** 那 1% 是拥有千万级用户和庞大工程团队的巨头。你读了他们的博客，觉得专用数据库 X 对他们很有效。但那是他们的规模、他们的团队、他们的问题。对其他人来说，Postgres 绰绰有余。

大多数人没意识到： **Postgres 扩展使用的算法与专用数据库相同，甚至更好。**

"专用"的溢价？大部分是营销。

| 需求场景 | 专用方案 | Postgres 扩展 | 算法是否相同？ |
| --- | --- | --- | --- |
| 全文搜索 | Elasticsearch | pg\_textsearch | ✅ 都用 BM25 |
| 向量搜索 | Pinecone | pgvector + pgvectorscale | ✅ 都用 HNSW/DiskANN |
| 时序数据 | InfluxDB | TimescaleDB | ✅ 都用时间分区 |
| 缓存 | Redis | UNLOGGED 表 | ✅ 都用内存存储 |
| 文档存储 | MongoDB | JSONB | ✅ 都用文档索引 |
| 地理空间 | 专用 GIS | PostGIS | ✅ 2001 年来的行业标准 |

这些不是阉割版。它们是 **相同甚至更好的算法** ，经过生产验证，开源，很多出自同一批研究者之手。

Benchmark 数据也能印证：

- **pgvectorscale** ：比 Pinecone <sup>[1]</sup> 延迟低 28 倍，成本降低 75%
- **TimescaleDB** ：性能持平甚至更优 InfluxDB，同时支持完整 SQL
- **pg\_textsearch** ：与 Elasticsearch 底层完全一致的 BM25 排序算法 <sup>[2]</sup>

### 隐性成本在累积

除了开发成本，数据库碎片化带来的复合成本也非常惊人:如果使用 七个数据库，那么备份与恢复、监控与告警、安全补丁、灾备演练 都需要准备七份，All in PG 则只需要一份就好。

**认知负担：** 你的团队需要掌握 SQL、Redis 命令、Elasticsearch Query DSL、MongoDB 聚合管道、Kafka 协议，以及 InfluxDB 那个非原生 SQL 的方言。这不叫专业化，这叫 **碎片化** 。

**数据一致性：** 让 Elasticsearch 和 Postgres 保持同步？你得写同步任务。同步会失败。数据会漂移。你加上对账逻辑。对账也会失败。最后你在维护基础设施而不是开发功能。

**SLA 算术：** 三个 99.9% 可用性的系统组合后可用性变成 99.7%。相当于每年 **26 小时停机** ，而不是 8.7 小时。每增加一个系统，故障概率都在相乘。

### 现代 Postgres 技术栈

这些扩展并不新，它们已在生产环境运行多年：

- **PostGIS** ：自 2001 年 <sup>[3]</sup> （24 年）。支撑 OpenStreetMap 和 Uber。
- **全文搜索** ：自 2008 年 <sup>[4]</sup> （17 年）。内置于 Postgres 核心。
- **JSONB** ：自 2014 年 <sup>[5]</sup> （11 年）。性能不输 MongoDB，还有 ACID。
- **TimescaleDB** ：自 2017 年 <sup>[6]</sup> （8 年）。GitHub 21K+ star。
- **pgvector** ：自 2021 年 <sup>[7]</sup> （4 年）。GitHub 19K+ star。

超过 48,000 家公司使用 PostgreSQL <sup>[8]</sup> ，包括 Netflix、Spotify、Uber、Reddit、Instagram 和 Discord。

### AI 时代的扩展

AI 浪潮催生了新一代扩展：

| 扩展 | 替代方案 | 亮点 |
| --- | --- | --- |
| pgvectorscale <sup>[9]</sup> | Pinecone, Qdrant | DiskANN 算法。延迟低 28 倍，成本降低 75%。 |
| pg\_textsearch <sup>[10]</sup> | Elasticsearch | 原生在 Postgres 中实现真正的 BM25 排序。 |
| pgai <sup>[11]</sup> | 外部 AI 流水线 | 数据变更时自动同步 embedding。 |

**这意味着什么：** 以前构建 RAG 应用需要 Postgres + Pinecone + Elasticsearch + 胶水代码。

现在？ **直接 Postgres。** 一个数据库。一种查询语言。一套备份。AI Agent 只需一条 Fork 命令就能启动测试环境。

### 快速上手：安装这些扩展

你只需要这些：

```
-- BM25 全文搜索
CREATE EXTENSION pg_textsearch;

-- AI 向量搜索
CREATE EXTENSION vector;
CREATE EXTENSION vectorscale;

-- AI embedding 与 RAG 工作流
CREATE EXTENSION ai;

-- 时序数据
CREATE EXTENSION timescaledb;

-- 消息队列
CREATE EXTENSION pgmq;

-- 定时任务
CREATE EXTENSION pg_cron;

-- 地理空间
CREATE EXTENSION postgis;
```

就这些。

### 代码示例

以下是各场景的可运行示例，按需跳转。

### 全文搜索（替代 Elasticsearch）

**扩展：** pg\_textsearch <sup>[12]</sup> （真正的 BM25 排序）

**你要替代的东西：**

- **Elasticsearch：** 独立 JVM 集群、复杂的映射配置、同步流水线、Java 堆调优
- **Solr：** 换汤不换药
- **Algolia：** 每 1000 次搜索 $1，依赖外部 API

**你得到的：** 驱动 Elasticsearch 的 **完全相同的 BM25 算法** ，直接在 Postgres 里运行。

```
-- 建表
CREATETABLE articles (
  id SERIAL PRIMARY KEY,
  title TEXT,
  content TEXT
);

-- 创建 BM25 索引
CREATE INDEX idx_articles_bm25 ON articles USING bm25(content)
WITH (text_config ='english');

-- 使用 BM25 评分搜索
SELECT title, -(content <@>'database optimization') as score
FROM articles
ORDERBY content <@>'database optimization'
LIMIT 10;
```

### 混合搜索（BM25 + 向量）

```
SELECT
  title,
-(content <@>'database optimization') as bm25_score,
  embedding <=> query_embedding as vector_distance,
0.7* (-(content <@>'database optimization')) +
0.3* (1- (embedding <=> query_embedding)) as hybrid_score
FROM articles
ORDERBY hybrid_score DESC
LIMIT 10;
```

这是 Elasticsearch 需要单独插件才能做到的事。在 Postgres 里，就是普通 SQL。

### 向量搜索（替代 Pinecone）

**扩展：** pgvector + pgvectorscale

**你要替代的东西：**

- **Pinecone：** 最低 $70/月，独立基础设施，数据同步头疼
- **Qdrant、Milvus、Weaviate：** 更多基础设施要管

**你得到的：** pgvectorscale <sup>[13]</sup> 使用 **DiskANN 算法** （微软研究院出品），在 99% 召回率下实现比 Pinecone **p95 延迟低 28 倍** 、 **吞吐量高 16 倍** 。

```
-- 启用扩展
CREATE EXTENSION vector;
CREATE EXTENSION vectorscale CASCADE;

-- 带 embedding 的表
CREATETABLE documents (
  id SERIAL PRIMARY KEY,
  content TEXT,
  embedding vector(1536)
);

-- 高性能索引（DiskANN）
CREATE INDEX idx_docs_embedding ON documents USING diskann(embedding);

-- 查找相似文档
SELECT content, embedding <=>'[0.1, 0.2, ...]'::vector as distance
FROM documents
ORDERBY embedding <=>'[0.1, 0.2, ...]'::vector
LIMIT 10;
```

**使用 pgai 自动同步 embedding：**

```
SELECT ai.create_vectorizer(
  'documents'::regclass,
  loading => ai.loading_column(column_name=>'content'),
  embedding => ai.embedding_openai(model=>'text-embedding-3-small', dimensions=>'1536')
);
```

现在每次 INSERT/UPDATE 都会自动重新生成 embedding。无需同步任务。没有数据漂移。不会被凌晨三点的告警叫醒。

### 时序数据（替代 InfluxDB）

**扩展：** TimescaleDB <sup>[14]</sup> （GitHub 21K+ star）

**你要替代的东西：**

- **InfluxDB：** 独立数据库，Flux 查询语言或非原生 SQL，SQL 支持有限
- **Prometheus：** 做指标监控不错，但不适合应用数据

**你得到的：** 自动时间分区、高达 90% 的压缩率、连续聚合。完整 SQL 支持。

```
-- 启用 TimescaleDB
CREATE EXTENSION timescaledb;

-- 建表
CREATETABLE metrics (
time TIMESTAMPTZ NOTNULL,
  device_id TEXT,
  temperature DOUBLE PRECISION
);

-- 转换为 hypertable
SELECT create_hypertable('metrics', 'time');

-- 按时间桶查询
SELECT time_bucket('1 hour', time) ashour,
       AVG(temperature)
FROM metrics
WHEREtime> NOW() -INTERVAL'24 hours'
GROUPBYhour;

-- 自动删除过期数据
SELECT add_retention_policy('metrics', INTERVAL'30 days');

-- 压缩（节省 90% 存储空间）
ALTERTABLE metrics SET (timescaledb.compress);
SELECT add_compression_policy('metrics', INTERVAL'7 days');
```

### 缓存（替代 Redis）

**特性：** UNLOGGED 表 + JSONB

```
-- UNLOGGED = 无 WAL 开销，写入更快
CREATE UNLOGGED TABLE cache (
  key TEXT PRIMARY KEY,
value JSONB,
  expires_at TIMESTAMPTZ
);

-- 带过期时间的写入
INSERTINTO cache (key, value, expires_at)
VALUES ('user:123', '{"name": "Alice"}', NOW() +INTERVAL'1 hour')
ON CONFLICT (key) DO UPDATESETvalue= EXCLUDED.value;

-- 读取
SELECTvalueFROM cache WHERE key ='user:123'AND expires_at > NOW();

-- 清理（用 pg_cron 定时调度）
DELETEFROM cache WHERE expires_at < NOW();
```

### 消息队列（替代 Kafka）

**扩展：** pgmq

**或原生 SKIP LOCKED 模式：**

```
CREATE TABLE jobs (
  id SERIAL PRIMARY KEY,
  payload JSONB,
  status TEXT DEFAULT'pending'
);

-- Worker 原子性地认领任务
UPDATE jobs SET status ='processing'
WHERE id = (
SELECT id FROM jobs WHERE status ='pending'
FORUPDATESKIP LOCKED LIMIT 1
) RETURNING *;
```

### 文档存储（替代 MongoDB）

**特性：** 原生 JSONB

```
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  data JSONB
);

-- 插入嵌套文档
INSERTINTO users (data) VALUES ('{
  "name": "Alice",
  "profile": {"bio": "Developer", "links": ["github.com/alice"]}
}');

-- 查询嵌套字段
SELECT data->>'name', data->'profile'->>'bio'
FROM users
WHERE data->'profile'->>'bio'LIKE'%Developer%';

-- 索引 JSON 字段
CREATE INDEX idx_users_email ON users ((data->>'email'));
```

### 地理空间（替代专用 GIS）

**扩展：** PostGIS

```
CREATE EXTENSION postgis;

CREATE TABLE stores (
  id SERIAL PRIMARY KEY,
  name TEXT,
  location GEOGRAPHY(POINT, 4326)
);

-- 查找 5km 内的门店
SELECT name, ST_Distance(location, ST_MakePoint(-122.4, 37.78)::geography) as meters
FROM stores
WHERE ST_DWithin(location, ST_MakePoint(-122.4, 37.78)::geography, 5000);
```

### 定时任务（替代 Cron）

**扩展：** pg\_cron

```
CREATE EXTENSION pg_cron;

-- 每小时执行
SELECT cron.schedule('cleanup', '0 * * * *',
  $$DELETE FROM cache WHERE expires_at < NOW()$$);

-- 每晚汇总
SELECT cron.schedule('rollup', '0 2 * * *',
  $$REFRESH MATERIALIZED VIEW CONCURRENTLY daily_stats$$);
```

### RAG 混合搜索

对于 AI 应用，你往往需要 **关键词搜索和语义搜索兼备** ：

```
-- 倒数排名融合（RRF）：结合关键词搜索 + 语义搜索
WITH bm25 AS (
SELECT id, ROW_NUMBER() OVER (ORDERBY content <@> $1) as rank
FROM documents LIMIT 20
),
vectors AS (
SELECT id, ROW_NUMBER() OVER (ORDERBY embedding <=> $2) as rank
FROM documents LIMIT 20
)
SELECT d.*,
1.0/(60+COALESCE(b.rank, 1000)) +
1.0/(60+COALESCE(v.rank, 1000)) as score
FROM documents d
LEFTJOIN bm25 b ON d.id = b.id
LEFTJOIN vectors v ON d.id = v.id
WHERE b.id ISNOTNULLOR v.id ISNOTNULL
ORDERBY score DESC LIMIT 10;
```

试试用 Elasticsearch + Pinecone 来做这件事。你需要两次 API 调用、结果合并、错误处理，还有双倍延迟。

在 Postgres 里：一条查询，一个事务，一个结果。

### 模糊搜索

**扩展：** pg\_trgm（Postgres 内置）

```
CREATE EXTENSION pg_trgm;

CREATE INDEX idx_name_trgm ON products USING GIN (name gin_trgm_ops);

-- 即使拼错也能找到 "PostgreSQL"
SELECT name FROM products
WHERE name % 'posgresql'
ORDER BY similarity(name, 'posgresql') DESC;
```

### 图遍历（替代图数据库）

**特性：** 递归 CTE

```
-- 查找某个经理下的所有下属（组织架构图）
WITHRECURSIVE org_tree AS (
SELECT id, name, manager_id, 1as depth
FROM employees WHERE id =42

UNIONALL

SELECT e.id, e.name, e.manager_id, t.depth +1
FROM employees e
JOIN org_tree t ON e.manager_id = t.id
WHERE t.depth <10
)
SELECT*FROM org_tree;
```

### 结论

还记得那个房子的比喻吗？你不会为了做顿晚饭就盖一栋餐厅，也不会为了停车就在城市另一头建个商业车库。你用的是家里的房间。

这就是我们在这里展示的。搜索、向量、时序数据、文档、队列、缓存——它们都是 Postgres 这栋房子里的房间。和专用数据库一样的算法。经过多年实战检验。被 Netflix、Uber、Discord 和其他 48,000 家公司使用。

**那 99% 怎么办？**

对 99% 的公司来说，Postgres 能处理你需要的一切。剩下的 1% 呢？那是当你需要在数百个节点上处理 PB 级日志，或者你需要 Kibana 特定的仪表盘，又或者你有某些确实超出 Postgres 能力的特殊需求。

但关键是： **你会知道自己是不是那 1%。** 不需要厂商的市场团队来告诉你。你自己会跑过 benchmark，碰到真正的瓶颈。

在那之前，不要因为有人告诉你"为正确的工作选择正确的工具"就把数据散落在七栋楼里。那句话卖的是数据库，不是为你服务的。

从 Postgres 开始。留在 Postgres。只在真正需要时才引入复杂度。

**2026 年了，用 Postgres 就够了。**

### 引用链接

\[1\]Pinecone: *https://www.tigerdata.com/blog/pinecone-alternatives*

\[2\]BM25 排序算法: *https://www.tigerdata.com/blog/you-dont-need-elasticsearch-bm25-is-now-in-postgres*

\[3\]2001 年: *https://en.wikipedia.org/wiki/PostGIS*

\[4\]2008 年: *https://en.wikipedia.org/wiki/PostgreSQL*

\[5\]2014 年: *https://en.wikipedia.org/wiki/PostgreSQL*

\[6\]2017 年: *https://github.com/timescale/timescaledb*

\[7\]2021 年: *https://github.com/pgvector/pgvector*

\[8\]48,000 家公司使用 PostgreSQL: *https://www.aventionmedia.com/technology-installed-base/postgresql-customers-list/*

\[9\]pgvectorscale: *https://github.com/timescale/pgvectorscale*

\[10\]pg\_textsearch: *https://github.com/timescale/pg\_textsearch*

\[11\]pgai: *https://github.com/timescale/pgai*

\[12\]pg\_textsearch: *https://github.com/timescale/pg\_textsearch*

\[13\]pgvectorscale: *https://github.com/timescale/pgvectorscale*

\[14\]TimescaleDB: *https://github.com/timescale/timescaledb*

继续滑动看下一个

萝卜要加油

向上滑动看下一个