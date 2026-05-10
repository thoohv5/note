---
title: ltree
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
source: http://www.postgres.cn/docs/12/ltree.html
---

## ltree

`ltree` 是 PostgreSQL 扩展，用于存储和查询树形层级标签路径。它适合组织架构、分类目录、评论楼层、权限路径等需要层级关系查询的场景。

## 安装

```sql
CREATE EXTENSION ltree;
```

## 基本类型

- `ltree`：标签路径，例如 `Top.Science.Astronomy`。
- `lquery`：路径匹配表达式。
- `ltxtquery`：全文式标签匹配表达式。

路径由标签组成，标签之间用点号分隔。标签通常由字母、数字和下划线构成。

## 常用操作

```sql
CREATE TABLE category (
    id bigserial PRIMARY KEY,
    path ltree NOT NULL
);

INSERT INTO category(path) VALUES
    ('Top'),
    ('Top.Book'),
    ('Top.Book.Database'),
    ('Top.Book.Database.PostgreSQL');
```

查询子树：

```sql
SELECT * FROM category
WHERE path <@ 'Top.Book'::ltree;
```

查询祖先：

```sql
SELECT * FROM category
WHERE path @> 'Top.Book.Database.PostgreSQL'::ltree;
```

模式匹配：

```sql
SELECT * FROM category
WHERE path ~ 'Top.*{1,2}'::lquery;
```

## 索引

层级路径查询应配合 GiST 或 GIN 索引：

```sql
CREATE INDEX category_path_gist_idx ON category USING gist(path);
CREATE INDEX category_path_gin_idx ON category USING gin(path);
```

实际选择应结合查询模式、数据规模和写入频率测试。

## 适用场景

- 层级分类和菜单。
- 组织部门树。
- 资源权限路径。
- 物料、地理、业务标签树。

## 注意事项

- 路径重命名会影响整棵子树，需要设计迁移策略。
- 层级频繁移动时要评估批量更新成本。
- 如果需要复杂图关系，`ltree` 不适合替代图数据库或闭包表。

## 关联

- [[PostgreSQL]]
- [[索引]]
- [[树结构]]
