# 如何将PG列更改为NULLABLE TRUE？

```bash
ALTER TABLE mytable ALTER COLUMN mycolumn DROP NOT NULL;
```