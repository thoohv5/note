# go-redis/redis

[https://github.com/go-redis/redis](https://github.com/go-redis/redis)

**go-redis模块自带连接池**，所有参数都是可选的，参数配置说明:

Options参数详解

```go
type Options struct {
    // 网络类型 tcp 或者 unix.
    // 默认是 tcp.
    Network string
    // redis地址，格式 host:port
    Addr string

    // 新建一个redis连接的时候，会回调这个函数
    OnConnect func(*Conn) error

    // redis密码，redis server没有设置可以为空。
    Password string
    
    // redis数据库，序号从0开始，默认是0，可以不用设置
    DB int

    // redis操作失败最大重试次数，默认不重试。
    MaxRetries int
    
    // 最小重试时间间隔.
    // 默认是 8ms ; -1 表示关闭.
    MinRetryBackoff time.Duration
    
    // 最大重试时间间隔
    // 默认是 512ms; -1 表示关闭.
    MaxRetryBackoff time.Duration

    // redis连接超时时间.
    // 默认是 5 秒.
    DialTimeout time.Duration
    
    // socket读取超时时间
    // 默认 3 秒.
    ReadTimeout time.Duration
    
    // socket写超时时间
    WriteTimeout time.Duration

    // redis连接池的最大连接数.
    // 默认连接池大小等于 cpu个数 * 10
    PoolSize int
    
    // redis连接池最小空闲连接数.
    MinIdleConns int
    // redis连接最大的存活时间，默认不会关闭过时的连接.
    MaxConnAge time.Duration
    
    // 当你从redis连接池获取一个连接之后，连接池最多等待这个拿出去的连接多长时间。
    // 默认是等待 ReadTimeout + 1 秒.
    PoolTimeout time.Duration
    // redis连接池多久会关闭一个空闲连接.
    // 默认是 5 分钟. -1 则表示关闭这个配置项
    IdleTimeout time.Duration
    // 多长时间检测一下，空闲连接
    // 默认是 1 分钟. -1 表示关闭空闲连接检测
    IdleCheckFrequency time.Duration

    // 只读设置，如果设置为true， redis只能查询缓存不能更新。
    readOnly bool
}

```

- MaxIdle: 最大空闲连接数；
    
    没有redis操作时依然可以保持这个连接数量，但要在IdleTimeout的时间范围内，不然就会关闭，结合IdleTimeout进行理解；
    
- IdleTimeout: 5 * 60 * time.Second，
    
    表示空闲连接保活时间，超过该时间后，连接会自动关闭，其实在默认情况下，当程序空闲时，redis连接池中是没有连接的；详情参见[github](https://github.com/go-redis/redis/issues/441)。
    
- MaxActive:最大连接数，一般为0，代表不限制；
    
    同一时间最多有这么多的连接，包括连接池中的连接以及连接池外的连接，假设最大空闲MaxIdle设置3，MaxActive设置4，那么，当连接池的连接全部处于忙碌状态时，如果这时候程序过来取连接，发现连接池没有取到，这时候就还可以再额外创建一个连接，当该连接关闭时，如果连接池中空闲数量小于3，则该连接会被放到连接池中，如果空闲数等于3，则该连接会直接关闭；