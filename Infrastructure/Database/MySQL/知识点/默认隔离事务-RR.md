# 默认隔离事务-RR

越高的隔离级别，能解决的数据一致性问题越多，理论上性能损耗更大，可并发性越低。隔离级别依次为

```go
SERIALIZABLE > RR > RC > Read-Uncommited
```

1. **RR支持next-key lock（gap lock & record lock）,RC 支持 record lock,RC的并发应该要好于RR；可以减少一部分锁竞争，减少死锁和锁超时的概率**
2. RC 隔离级别不支持 statement 格式的bin log，因为该格式的复制，会导致主从数据的不一致；只能使用 mixed 或者 row 格式的bin log; 这也是为什么MySQL默认使用RR隔离级别的原因。复制时，我们最好使用：binlog_format=rowMySQL5.6 的早期版本，RC隔离级别是可以设置成使用statement格式的bin log，后期版本则会直接报错；