---
title: UUID
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## UUID

```sql
select * from pg_extension;
\dx
```

### 数据库插件

```sql
yum -y install e2fsprogs-devel uuid uuid-devel libuuid-devel

make && make install

create extension "uuid-ossp"
```

```sql
create or replace function random_string(integer)
returns text as
$body$
   select array_to_string(array(select substring('0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz' FROM (ceil(random()*62))::int FOR 1) FROM generate_series(1, $1)), '');
$body$
language sql volatile;
```