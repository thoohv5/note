# How disable postgresql "Cache" optimization?

URL: https://stackoverflow.com/questions/24252455/how-disable-postgresql-cache-optimization

![](https://cdn.sstatic.net/Sites/stackoverflow/Img/apple-touch-icon@2.png?v=73d79a89bded)

PostgreSQL doesn't have a "cache" optimisation, in the sense of a query result cache.
PostgreSQL没有“缓存”优化，即查询结果缓存。

It does cache table blocks that were recently read in `shared_buffers`, but for most installs that only has a small effect. The main cache is the operating system's disk read cache. For more information see:
它确实缓存了最近读入 `shared_buffers` 的表块，但对于大多数安装来说，这只有很小的影响。主缓存是操作系统的磁盘读取缓存。有关详细信息，请参阅：

[See and clear Postgres caches/buffers?](https://stackoverflow.com/q/1216660/398670)
[查看并清除 Postgres 缓存/缓冲区？](https://stackoverflow.com/q/1216660/398670)

It sounds to me like you have a system with a reasonable amount of RAM and a fast CPU but a terribly slow disk. So queries that only hit the OS's disk cache are very fast, but queries that go to disk take a couple of seconds to read the data in. So caching effects are very strong.
在我看来，您有一个系统，具有合理数量的 RAM 和快速的 CPU，但磁盘速度非常慢。因此，仅命中操作系统磁盘缓存的查询速度非常快，但进入磁盘的查询需要几秒钟才能读入数据。所以缓存效果非常强。

You should `explain (buffers, analyze, verbose) SELECT ...` your queries. Try with a couple of different input values until you get a slow one. Compare plans.
你应该 `explain (buffers, analyze, verbose) SELECT ...` 你的查询。尝试使用几个不同的输入值，直到得到一个缓慢的输入值。比较计划。

If the plans are the same, that's probably it.
如果计划相同，可能就是这样。

If the plans are different, you're probably hitting a situation where the query planner is making bad choices based on variations in the table statistics. Increasing the statistics targets for the columns of interest may help (see the manual). If you get different plans and are stuck / want help, feel free to post a new question on dba.stackexchange.com with details.
如果计划不同，则可能会遇到查询规划器根据表统计信息的变化做出错误选择的情况。增加相关列的统计目标可能会有所帮助（请参阅手册）。如果您有不同的计划并且遇到困难/需要帮助，请随时在 dba.stackexchange.com 上发布新问题并附上详细信息。