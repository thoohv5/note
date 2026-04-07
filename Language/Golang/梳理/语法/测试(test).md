# 测试(test)

[stretchr/testify](http://github.com/stretchr/testify)

[agiledragon/gomonkey](http://github.com/agiledragon/gomonkey)

```sql
mockgen -destination=mock_redis.go -package=unit github.com/gomodule/redigo/redis Conn
```

# 覆盖率

```sql
go test -v -cover -run ${test文件内函数名}
go test -v -cover -gcflags=all=-l -coverprofile=coverage.out
```

[手把手教你如何进行 Golang 单元测试](https://mp.weixin.qq.com/s/N5wby-aWWEPc7mHN_lN3lQ)