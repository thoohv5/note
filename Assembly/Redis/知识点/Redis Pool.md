# Redis Pool

# 错误

connection pool exhausted

连接池耗尽

```go
#redigo
// Handle limit for p.Wait == false.
if !p.Wait && p.MaxActive > 0 && p.active >= p.MaxActive {
	p.mu.Unlock()
	return nil, ErrPoolExhausted
}
```

如果没有配置为等待模式。且配置了连接池的最大活跃个数 如果当前活跃个数大于配置的最大活跃 则返回连接池耗尽的错误。所以需要调大这个MaxActive参数。

```go
func (p *Pool) put(pc *poolConn, forceClose bool) error {
 	p.mu.Lock()
 	if !p.closed && !forceClose {
 		pc.t = nowFunc()
 		p.idle.pushFront(pc)
 		if p.idle.count > p.MaxIdle {
 			pc = p.idle.back
 			p.idle.popBack()
 		} else {
 			pc = nil
 		}
 	}
 	if pc != nil {
 		p.mu.Unlock()
 		pc.c.Close()
 		p.mu.Lock()
 		p.active--
 	}
 	if p.ch != nil && !p.closed {
 		p.ch <- struct{}{}
 	}
 	p.mu.Unlock()
 	return nil
 }
```

连接池的具体实现是通过一个链表来实现的。如果发现连接池里面的空闲个数超过了MaxIdle，就会把尾部的连接删除 把最新的连接放到头部。类似将老的连接删掉，加入最新的。

```bash
# 查看连接数
info clients

# 查看当前节点允许的最大连接数可用，默认10000
config get maxclients

netstat -ano

Redis服务器默认设置的最大连接数maxclients是10000，但是受服务器最大文件数影响，服务器默认最大文件数是1024，所以Redis最大连接也为1024-32=992，由于网络原因或者连接未正常关闭导致Redis服务器连接数接近990左右，应用程序连不上Redis

```