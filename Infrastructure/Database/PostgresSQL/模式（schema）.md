# 模式（schema）

### 创建

```sql
CREATE SCHEMA {schema};
```

### 删除

```sql
DROP SCHEMA {schema};
DROP SCHEMA {schema} CASCADE;
```

### 搜索路径

```sql
SHOW search_path;
SET search_path TO {schema},public;
```