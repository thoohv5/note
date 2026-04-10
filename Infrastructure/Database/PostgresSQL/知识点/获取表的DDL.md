# 获取表的DDL

```sql
pg_dump -U postgres -d postgres -s -t {table_name} | egrep -v "^--|^$|^SET";
```