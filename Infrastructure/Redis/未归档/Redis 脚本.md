---
title: Redis 脚本
date: 2026-04-07
  - 基础设施
  - Redis
type: guide
status: complete
---

## Redis 脚本

## 简介

Redis脚本使用Lua解释器来执行脚本。Redis2.6版本通过内嵌支持Lua环境。执行命令EVAL

## 作用

1. 减少网络开销：使用脚本，减少了网络往返时延
2. 原子操作：Redis会将整个脚本作为一个整体执行，中间不会被其他命令插入
3. 复用：客户端发送的脚本会永久存储在Redis中，意味着其他客户端可以复用这一脚本而不需要使用代码完成相同的逻辑

## 执行方式

### 文件

```lua
redis-cli --eval (lua file) key [key ...] , arg [arg ...]
```

1. lua file 执行的脚本
2. 脚本参数KEYS&ARGV

<aside>
💡 ","两边的空格不能省略，否则会出错

</aside>

### 命令

```lua
EVAL script numkeys key [key ...] arg [arg ...]
```

```lua
## 将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本
SCRIPT LOAD script

## 根据给定的 sha1 校验码，执行缓存在服务器中的脚本
EVALSHA sha1 numkeys key [key ...] arg [arg ...]

## 校验指定的脚本是否已经被保存在缓存当中
SCRIPT LOAD script

## 杀死当前正在运行的 Lua 脚本，当且仅当这个脚本没有执行过任何写操作时，这个命令才生效
SCRIPT KILL
```

## 常用命令

`-x`选项从标准输入（stdin）读取最后一个参数

```lua
## 比如从管道中读取输入
echo "thooh" | redis-cli -x set name
```

`r`选项重复执行一个命令指定的次数。
`-i`设置命令执行的间隔。

```lua
## 比如查看redis每秒执行的commands（qps）
redis-cli -r 100 -i 1 info stats | grep instantaneous_ops_per_sec
```

`--scan`命令扫描redis中的key，`--pattern`选项指定扫描的key的pattern （非阻塞）

```lua
redis-cli --scan --pattern 'n_*'
```

`—-bigkeys` 

对Redis中的key进行采样，寻找较大的keys（非阻塞）

```lua
redis-cli --bigkeys
```