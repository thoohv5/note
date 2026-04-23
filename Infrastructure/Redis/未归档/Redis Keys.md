---
title: Redis Keys
date: 2026-04-07
  - 基础设施
  - Redis
type: note
status: complete
---

## Redis Keys

- `KEYS` 命令的性能随着数据库数据的增多而越来越慢
- `KEYS` 命令会引起阻塞，连续的 `KEYS` 命令足以让 Redis 阻塞

### xargs

xargs命令是给其他命令传递参数的一个过滤器，也是组合多个命令的一个工具。它擅长将标准输入数据转换成命令行参数，xargs能够处理管道或者stdin并将其转换成特定命令的命令参数。xargs也可以将单行或多行文本输入转换为其他格式，例如多行变单行，单行变多行。xargs的默认命令是echo，空格是默认定界符。这意味着通过管道传递给xargs的输入将会包含换行和空白，不过通过xargs的处理，换行和空白将被空格取代。xargs是构建单行命令的重要组件之一。

### **SCAN 命令**

Redis从2.8版本开始支持scan命令，SCAN命令的基本用法如下：

- 复杂度虽然也是 O(n)，通过游标分步进行不会阻塞线程;
- 有限制参数 COUNT ；
- 同 keys命令 一样提供模式匹配功能;

```bash
SCAN cursor [MATCH pattern] [COUNT count]
```

- scan 命令提供三个参数，第一个是cursor，第二个是要匹配的正则，**第三个是单次遍历的槽位**
- 第一个遍历是 cursor 值为0，然后将返回结果的第一个整数作为下一个遍历的游标，如果最后返回的到cursor的值为0就代表结束。

[SCAN](http://doc.redisfans.com/key/scan.html#scan) 命令及其相关的 [SSCAN](http://doc.redisfans.com/set/sscan.html#sscan) 命令、 [HSCAN](http://doc.redisfans.com/hash/hscan.html#hscan) 命令和 [ZSCAN](http://doc.redisfans.com/sorted_set/zscan.html#zscan) 命令都用于增量地迭代（incrementally iterate）一集元素（a collection of elements）：

- [SCAN](http://doc.redisfans.com/key/scan.html#scan) 命令用于迭代当前数据库中的数据库键。
- [SSCAN](http://doc.redisfans.com/set/sscan.html#sscan) 命令用于迭代集合键中的元素。
- [HSCAN](http://doc.redisfans.com/hash/hscan.html#hscan) 命令用于迭代哈希键中的键值对。
- [ZSCAN](http://doc.redisfans.com/sorted_set/zscan.html#zscan) 命令用于迭代有序集合中的元素（包括元素成员和元素分值）。

```bash
redis-cli --raw keys "key前缀*" | xargs redis-cli del
redis-cli --scan --pattern "key前缀*" | xargs -L 1000 redis-cli del
```

> 备注
> 

增量式迭代命令也不是没有缺点的： 举个例子， 使用 SMEMBERS 命令可以返回集合键当前包含的所有元素， 但是对于 SCAN 这类增量式迭代命令来说， 因为在对键进行增量式迭代的过程中， 键可能会被修改， 所以增量式迭代命令只能对被返回的元素提供有限的保证 （offer limited guarantees about the returned elements）