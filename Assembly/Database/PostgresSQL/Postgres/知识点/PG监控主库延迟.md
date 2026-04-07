# PG监控主库延迟

### 通过WAL延迟时间衡量

```
select pid,usename,client_addr,state,write_lag,flush_lag,replay_lag from pg_stat_replication;

```

### 通过WAL日志应用延迟量衡量

```
select pid,usename,client_addr,state,
pg_wal_lsn_diff(pg_current_wal_lsn(),write_lsn) write_delay,
pg_wal_lsn_diff(pg_current_wal_lsn(),flush_lsn) flush_delay,
pg_wal_lsn_diff(pg_current_wal_lsn(),replay_lsn) replay_dely
from pg_stat_replication;

```

# 附录

[http://blog.itpub.net/69990629/viewspace-2846602/](http://blog.itpub.net/69990629/viewspace-2846602/)