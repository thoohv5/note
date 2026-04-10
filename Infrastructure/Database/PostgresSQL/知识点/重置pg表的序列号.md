# 重置pg表的序列号

```bash
select setval('表名_id_seq',(select max(id) from 表名));
```