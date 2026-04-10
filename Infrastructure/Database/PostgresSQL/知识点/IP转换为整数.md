# IP转换为整数

```sql
SELECT ('127.0.0.1'::inet - '0.0.0.0'::inet) as ip_integer
```