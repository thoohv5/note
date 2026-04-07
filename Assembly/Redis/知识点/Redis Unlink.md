# Redis Unlink

DEL vs UNLINK （4.0.0）

```c
UNLINK key [key ...]
Available since 4.0.0.
Time complexity: O(1) for each key removed regardless of its size. Then the command does O(N) work in a different thread in order to reclaim memory, where N is the number of allocations the deleted objects where composed of.

This command is very similar to DEL: it removes the specified keys. Just like DEL a key is ignored if it does not exist. However the command performs the actual memory reclaiming in a different thread, so it is not blocking, while DEL is. This is where the command name comes from: the command just unlinks the keys from the keyspace. The actual removal will happen later asynchronously.
```

# 附录

[Redis 4.0 非阻塞删除](http://www.russellluo.com/2018/08/async-del-since-redis-4-0.html)