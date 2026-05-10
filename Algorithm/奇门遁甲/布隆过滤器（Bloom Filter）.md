---
title: 布隆过滤器（Bloom Filter）
date: 2026-04-07
tags:
  - 算法
  - 奇门遁甲
type: guide
status: complete
---

## 布隆过滤器（Bloom Filter）

## 概念

bloom算法类似一个hash set，用来判断某个元素（key）是否在某个集合中

无需存储key的值，对于每个key，只需要k个比特位，每个存储一个标志，用来判断key是否在集合中

布隆过滤器可以判断某个数据一定不存在，但是无法判断一定存在

## 算法

1. 首先需要k个hash函数，每个函数可以把key散列成为1个整数

2. 初始化时，需要一个长度为n比特的数组，每个比特位初始化为0

3. 某个key加入集合时，用k个hash函数计算出k个散列值，并把数组中对应的比特位置为1

4. 判断某个key是否在集合时，用k个hash函数计算出k个散列值，并查询数组中对应的比特位，如果所有的比特位都是1，认为在集合中。

### 优点

不需要存储key，节省空间

### 缺点

1. 算法判断key在集合中时，有一定的概率key其实不在集合中

2. 无法删除

## 应用场景

某些存储系统的设计中，会存在空查询缺陷：当查询一个不存在的key时，需要访问慢设备，导致效率低下。

## 附录

[李玉龙](https://www.cnblogs.com/liyulong1982/p/6013002.html)

[RedisBloom/RedisBloom](https://github.com/RedisBloom/RedisBloom#launch-redisbloom-with-docker)

[布谷鸟过滤器：实际上优于布隆过滤器](http://www.linvon.cn/posts/cuckoo/)

[](https://zhuanlan.zhihu.com/p/462813998)