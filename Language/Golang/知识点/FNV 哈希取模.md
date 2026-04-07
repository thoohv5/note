# FNV 哈希取模

# 概述

FNV哈希是一种非加密型哈希函数，由Glenn Fowler、Landon Curt Noll和Phong Vo共同设计。Golang标准库中提供了两种FNV实现：fnv32和fnv64。它的特点就是计算速度快，哈希分布均匀，实现简单以及适合非加密场景。

## 示例

```go
// ShardByFNV 使用FNV哈希取模进行分流
func ShardByFNV(key string, shardCount int) int {	
	hasher := fnv.New32a()	
	hasher.Write([]byte(key))	
	hashValue := hasher.Sum32()       
	return int(hashValue % uint32(shardCount))
}
```