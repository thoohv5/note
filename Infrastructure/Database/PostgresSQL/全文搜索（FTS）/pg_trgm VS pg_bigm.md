# pg_trgm VS pg_bigm

[[https://github.com/digoal/blog/blob/master/202009/20200912_01]]

## pg_trgm

将对象进行切词，每3个字节为一组进行切割，所以单、双字节的就无法覆盖到。

x% 至少需要1个字符，%x至少需要2个字符，%x%至少需要3个字符

索引空间较大

支持ilike

```sql
create table t_trgm_test (id int,info varchar(60));
insert into t_trgm_test(id, info) select id, info from t_bigm_test;
create index idx_t_trgm_test on t_trgm_test using gin(info gin_trgm_ops);

select pg_size_pretty(pg_relation_size('idx_t_trgm_test')); # 332 MB

explain analyze select * from t_trgm_test where info like '%3'; # Seq Scan
explain analyze select * from t_trgm_test where info like '%3d'; # Bitmap Heap Scan
explain analyze select * from t_trgm_test where info like '%3dc'; # Bitmap Heap Scan

explain analyze select * from t_trgm_test where info like '3%'; # Bitmap Heap Scan
explain analyze select * from t_trgm_test where info like '3a%'; # Bitmap Heap Scan
explain analyze select * from t_trgm_test where info like '3ab%'; # Bitmap Heap Scan

explain analyze select * from t_trgm_test where info like '%3%'; # Seq Scan
explain analyze select * from t_trgm_test where info like '%3a%'; # Seq Scan
explain analyze select * from t_trgm_test where info like '%3ab%'; # Bitmap Heap Scan

explain analyze select * from t_trgm_test where info ilike '%3ab%'; # Bitmap Heap Scan
```

## pg_bigm

将对象进行切词，每2个字节为一组进行切割，可以支持任意字数模糊查询

x% 至少需要1个字符，%x至少需要1个字符，%x%至少需要2个字符

索引空间较小

不支持ilike

```sql
create table t_bigm_test (id int,info varchar(60));
insert into t_bigm_test select generate_series(1,5000000) ,md5(random()::varchar) ;
create index idx_t_bigm_test on t_bigm_test using gin(info gin_bigm_ops);

select pg_size_pretty(pg_relation_size('idx_t_bigm_test')); # 173 MB

explain analyze select * from t_bigm_test where info like '%3'; # Bitmap Heap Scan
explain analyze select * from t_bigm_test where info like '%3d'; # Bitmap Heap Scan
explain analyze select * from t_bigm_test where info like '%3dc'; # Bitmap Heap Scan

explain analyze select * from t_bigm_test where info like '3%'; # Bitmap Heap Scan
explain analyze select * from t_bigm_test where info like '3a%'; # Bitmap Heap Scan
explain analyze select * from t_bigm_test where info like '3ab%'; # Bitmap Heap Scan

explain analyze select * from t_bigm_test where info like '%3%'; # Seq Scan
explain analyze select * from t_bigm_test where info like '%3a%'; # Bitmap Heap Scan
explain analyze select * from t_bigm_test where info like '%3ab%'; # Bitmap Heap Scan

explain analyze select * from t_bigm_test where info ilike '%3ab%'; # Gather
```