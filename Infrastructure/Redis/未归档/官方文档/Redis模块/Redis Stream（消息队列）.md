---
title: Redis Stream（消息队列）
date: 2026-04-07
  - 基础设施
  - Redis
type: guide
status: complete
---

## Redis Stream（消息队列）

## 概述

Redis5.0 中还增加了一个数据类型[Stream](https://so.csdn.net/so/search?q=Stream&spm=1001.2101.3001.7020)，它借鉴了Kafka的设计，是一个新的强大的支持多播的可持久化的[消息队列](https://so.csdn.net/so/search?q=消息队)97&spm=1001.2101.3001.7020

用过Redis做消息队列的都了解，基于Reids的消息队列实现有很多种，例如：

- **PUB/SUB，订阅/发布模式**
    - 但是发布订阅模式是**无法持久化**的，如果出现网络断开、Redis 宕机等，消息就会被丢弃；
- 基于**List LPUSH+BRPOP** 或者 **基于Sorted-Set**的实现
    - 支持了持久化，但是**不支持多播，分组消费**等

### **特征：**

1. **数据结构**：Redis Stream是一个由有序消息组成的日志数据结构，每个消息都有一个全局唯一的ID，确保消息的顺序性和可追踪性。
2. **消息ID**：消息的ID由两部分组成，分别是毫秒级时间戳和序列号。这种设计确保了消息ID的单调递增性，即新消息的ID总是大于旧消息的ID。
3. **消费者组**：Redis Stream支持消费者组的概念，允许多个消费者以组的形式订阅Stream，并且每个消息只会被组内的一个消费者处理，避免了消息的重复消费。

### **优势:**

1. **持久化存储**：Stream中的消息可以被持久化存储，确保数据不会丢失，即使在Redis服务器重启后也能恢复消息。
2. **有序性**：消息按照产生顺序生成消息ID, 被添加到Stream中，并且可以按照指定的条件检索消息，保证了消息的有序性。
3. **多播与分组消费**：支持多个消费者同时消费同一流中的消息，并且可以将消费者组织成消费组，实现消息的分组消费。
4. **消息确认机制**：消费者可以通过XACK命令确认是否成功消费消息，保证消息至少背消费一次,确保消息不会被重复处理。
5. **阻塞读取**：消费者可以选择阻塞读取模式，当没有新消息时，消费者会等待直至新消息到达。
6. **消息可回溯**: 方便补数、特殊数据处理, 以及问题回溯查询

## **命令**

1. **XADD**：向Stream中添加消息。**如果指定的Stream不存在，则会自动创建**。
2. **XREAD**：以阻塞/非阻塞方式获取Stream中的消息列表。
3. **XREADGROUP**：从消费者组中读取消息，支持阻塞读取。
4. **XACK**：确认消费者已经成功处理了消息。
5. **XGROUP**：用于管理消费者组，包括创建、设置ID、销毁消费者组等操作。
6. **XPENDING**：查询消费者组中的待处理消息。
7. XRANGE: 查询消息
8. XINFO：查询信息

### **XADD 消息记录**

XADD命令用于向Redis Stream（流）数据结构中添加消息。

**XADD 命令的基本语法**

```bash
XADD <stream> [MAXLEN maxlen] [ID id] field1 value1 [field2 value2 ...]
```

1. **`stream_name`**：指定要添加消息的Stream的名字。
2. **`MAXLEN maxlen**：`可选参数，用于限制Stream的最大长度。当Stream的长度达到maxlen时，旧的消息会被自动删除。
3. **`ID id`**：可选参数，用于指定消息的ID。如果不指定该参数，Redis会自动生成一个唯一的ID。
4. **`field1 value1 [field2 value2 ...]`**：消息的字段和值，消息的内容以key-value的形式存在。

XADD命令的一个重要用途是实现消息发布功能，发布者可以使用XADD命令向Stream中添加消息。

- **XADD 示例**
    
    假设我们有一个名为`userinfo_stream`的Stream，并希望向其中添加一个包含`sensor_id`和`temperature`字段的消息，我们可以使用以下命令：
    
    ```bash
    XADD userinfo_stream * user_name brand age 18
    ```
    
    在这个例子中，`*`表示让Redis自动生成一个唯一的消息ID。消息包含两个字段：`username`和`age`，它们的值分别是`brand`和`18`。所以这边记录了一个用户信息，姓名为`brand`， 年龄`18`岁。
    
    ### **注意**
    
    - 如果指定的Stream**不存在**，XADD命令会**创建一个新的Stream**。
    - 消息的ID是唯一的，并且Redis会保证。如果指定了ID，则新消息的ID必须大于Stream中现有的所有消息的ID。
        
        **Stream中消息的ID是单调递增的**
        
    - 使用，这在处理大量消息时非常有用，可以避免Stream占用过多的内存或磁盘空间。
        
        **MAXLEN参数可以限制Stream的大小**
        

### 普通模式

### **XREAD 消息消费**

即将消息从队列中读取出来（消费）

**XREAD 命令的基本语法**

XREAD命令的基本语法如下：

```bash
XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] ID [ID ...]
```

1. **`COUNT count`**：可选参数，用于指定一次读取的最大消息数量。如果不指定，默认为1。
2. **`BLOCK milliseconds`**：这也是一个可选参数，用于指定阻塞的时间（以毫秒为单位）。如果指定了阻塞时间，并且当前没有可消费的消息，客户端将在指定的时间内阻塞等待。如果不设置该参数或设置为0，则命令将立即返回，无论是否有可消费的消息。
3. **`STREAMS key [key ...] ID [ID ...]`**：这部分指定了要消费的流（Streams）和对应的起始消息ID。可以一次指定多个流和对应的起始ID。

**XREAD命令的工作机制**

1. **读取指定ID之后的消息**：XREAD命令会返回指定ID之后的消息（不包含指定ID的消息本身）。如果没有指定ID，或者指定的ID不存在于流中，那么命令将从流的开始或结束处读取消息，具体取决于ID的值（**如“0-0”表示从流的开始处读取，“$”表示从流的当前最大ID处读取**）。
    
    **Redis Stream 中 XREAD 的 ID 规则**
    
    | ID 写法 | 含义 | 适用场景 |
    | --- | --- | --- |
    | `"0-0"` | 从**头开始读取所有历史消息** | 用于一次性读取完整历史 |
    | `"$"` | 从**最新消息开始（不含历史）** | 作为“订阅模式”，只关心新消息 |
    | `"last_id"`（例如 `"1700000000000-0"`） | 从指定 ID **之后** 开始读取 | 用于断点续读 |
    | `">"` | **仅 XREADGROUP 使用**：表示消费未被其他消费者领取的新消息 |  |
2. **阻塞读取**：当设置了BLOCK参数后，如果当前没有可消费的消息，客户端将进入阻塞状态，直到有新的消息到达或阻塞时间超时。这种机制非常适合实现消费者等待生产者产生新消息的场景。
3. **支持多个流**：XREAD命令支持同时从多个流中读取消息，只需在命令中指定多个流和对应的起始ID即可。

### **XREAD 示例**

假设我们有一个名为`userinfo_stream`的流，并且想要从该流中读取消息。以下是一些示例：

1. **非阻塞读取最新消息**：

```bash
XREAD COUNT 1 STREAMS userinfo_stream $
```

这条命令会尝试从`userinfo_stream`流中读取最新的消息（如果有的话）。`$`是一个特殊ID，表示流的当前最大ID。

1. **阻塞读取最新消息**：

```bash
XREAD COUNT 1 BLOCK 1000 STREAMS userinfo_stream $
```

这条命令会阻塞1000毫秒，等待`userinfo_stream`流中出现新的消息。如果在1000毫秒内有新消息到达，则命令会返回该消息；否则，命令将超时并返回nil。

1. **从特定ID开始读取**：

```bash
XREAD COUNT 2 STREAMS userinfo_stream 1722159931000-0
1) 1) "userinfo_stream"
    2)  1) 1) "1722159931000-0"
         2) 1) "user_name"
             2) "brand"
             3) "age"
             4) "18"
```

这条命令会从`userinfo_stream`流中读取ID大于或等于`1722159931000-0`的消息，最多返回数据。

### **注意**

1. **消息ID的唯一性**：在Redis Streams中，每个消息都有一个全局唯一的消息ID，这个消息ID由两部分组成：时间戳和序列号。时间戳表示消息被添加到流中的时间，序列号表示在同一时间戳内添加的消息的顺序。
2. **消费者组**：虽然XREAD命令本身不直接涉及消费者组的概念，但Redis Streams还支持消费者组模式，允许一组消费者协作消费同一流中的消息。在消费者组模式下，通常会使用XREADGROUP命令而不是XREAD命令来读取消息。
3. **性能考虑**：XREAD命令在读取大量消息时可能会消耗较多的CPU和内存资源。因此，在实际应用中需要根据实际情况合理设置COUNT参数的值，避免一次性读取过多消息导致性能问题。

### **Consumer Group 消费组模式**

典型的多播模式，在实时性要求比较高的场景，如果你想加快对消息的处理。那这是一个不错的选择，我们让队列在逻辑上进行分区，用不同的消费组来隔离消费。所以：

![](https://img2024.cnblogs.com/blog/167509/202408/167509-20240803112807618-1287213660.png)

消费者组允许多个消费者（client 或 process）协同处理同一个流（Stream）中的消息。每个消费者组维护自己的消费偏移量（即已处理消息的位置），以支持消费者之间的负载均衡和容错。

### **创建消费者组(XGROUP CREATE)**

使用 XGROUP CREATE 命令创建消费者组。

```bash
## stream_name：队列名称
## consumer_group：消费者组
## msgIdStartIndex：消息Id开始位置
## msgIdStartIndex：消息Id结束位置
## $ 表示从流的当前末尾（即最新消息）开始创建消费者组。如果流不存在，MKSTREAM 选项将自动创建流
XGROUP CREATE <stream> <group> <message-id>-<message-id>
## 或者
XGROUP CREATE <stream> <group> $ MKSTREAM
```

- 下面是具体实现示例，为队列 userinfo_stream 创建了消费组1（consumer_group1）和 消费组2（consumer_group2）：
    
    ```lua
    > xgroup create userinfo_stream consumer_group 0-0
    OK
    > xgroup create userinfo_stream consumer_group 0-0
    OK
    ```
    

### **读取消息(XREADGROUP)**

消费者可以通过 `XREADGROUP` 命令从消费者组中读取消息。`XREADGROUP` 命令不仅读取消息，还会更新消费者组中的消费者状态，即标记哪些消息已被读取。

```bash
## group_name: 消费者群组名
## consumer_name: 消费者名称
## COUNT number: count 消费个数
## BLOCK ms: 表示如果流中没有新消息，则命令将阻塞最多 xx 毫秒，0则无限阻塞
## stream_name: 队列名称
## id: 消息消费ID
## []：代表可选参数
## `>`：放在命令参数的最后面，表示从尚未被消费的消息开始读取；

XREADGROUP GROUP group_name consumer_name [COUNT number] [BLOCK ms] STREAMS stream_name [stream ...] id [id ...]
## 或者
XREADGROUP GROUP group_name consumer_name COUNT 1 BLOCK 2000 STREAMS stream_name >
```

- 下面是具体实现示例，消费组 consumer_group1 的消费者 consumer1 从 userinfo_stream 中以阻塞的方式读取一条消息：
    
    ```bash
    XREADGROUP GROUP consumer_group consumer COUNT 1 BLOCK 0 STREAMS userinfo_stream >
    1) 1) "userinfo_stream"
       2) 1) 1) "1722159931000-0"
             2) 1) "user_name"
                2) "brand"
                3) "age"
                4) "18"
    
    ```
    

### **确认消息(XACK)**

处理完消息后，消费者需要发送 XACK 命令来确认消息。这告诉 Redis 这条消息已经被成功处理，并且可以从消费者组的待处理消息列表中移除

```bash
## stream_name: 队列名称
## group_name: 消费者群组名
## <message-id> 是要确认的消息的 ID。

XACK <stream> <group> <message-id>
```

```bash
## ACK 确认两条消息
XACK userinfo_stream consumer_group 1722159931000-0 1722159932000-0
(integer) 2
```

### **消息可靠性保障(PLE)**

**PEL（Pending Entries List）**记录了当前被消费者读取但尚未确认（ACK）的消息。这些消息在消费者成功处理并发送ACK命令之前，会一直保留在PEL中。如果消费者崩溃或未能及时发送ACK命令，Redis将确保这些消息能够被重新分配给其他消费者进行处理，从而实现消息的可靠传递。

```bash
XPENDING <stream> <group>
```

- 以下的例子中，我们查看 `userinfo_stream` 中的 消费组 `consumer_group1` 中各个消费者已读取但未确认的消息信息。
    
    ```bash
    XPENDING userinfo_stream consumer_group
    1) (integer) 2   # 未确认消息条数
    2) "1722159931000-0"
    3) "1722159932000-0"
    ```
    

### 抢占闲置消息(XCLAM)

- 消费组（XREADGROUP）消费消息后，如果消费者处理失败或者宕机，这条消息会一直在 PEL 里，标记为 **pending（未 ACK）**。
- `XCLAIM` 可以把这些消息 **重新分配给新的消费者**，保证消息不会丢失。

```sql
XCLAIM <stream> <group> <consumer> <min-idle-time> <id> [ID ...] [OPTIONS]
```

参数	作用
<stream>	Stream 名称
<group>	消费组名
<consumer>	**新消费者名（接管消息）**
<min-idle-time>	消息至少未被 ACK 的时间（毫秒），保证只接管“闲置”的消息
<id>	PEL 中消息的 ID（可以一次多个）
OPTIONS	可选参数：JUSTID（只返回 ID）、FORCE（忽略 min-idle-time）

- 例子
    
    ```sql
    XCLAIM userinfo_stream consumer_group consumer 60000 1760498078428-0
    ```
    

### 查询（XRANGE）

```sql
XRANGE userinfo_stream - +
XINFO STREAM userinfo_stream
XINFO GROUPS consumer_group
XINFO CONSUMERS userinfo_stream consumer_group
```

## 附录

详细的stream操作见官网文档：[https://redis.io/docs/data-types/streams-tutorial/](https://redis.io/docs/data-types/streams-tutorial/)