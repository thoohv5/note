---
title: JetStream
date: 2026-04-07
tags: [微服务, 分布式]
type: note
status: complete
---

# JetStream

JetStream是NATS内置的**分布式持久化存储系统**，构建在JetStream存储系统上的“core NATS”功能更多，消息可靠性更高。

![[Pasted image 20231122171113.png]]

## Stream

```bash
nats stream add  "TEST" \\\\   # Stream名称
			--subjects "test.*" \\\\ # 订阅主题
			--storage file \\\\      # 存储类型:file/memory
      --retention limit \\\\   # 持久化的方式:limits/interest/workq
      --discard=old \\\\       # 丢弃消息：old/new,消息数量达到limits限制数量后，消息如何处理
      --max-msgs=-1 \\\\       # 最大消息条数，-1为无限制
      --max-msgs-per-subject=-1 \\\\ #主题最大消息条数，-1为无限制
      --max-bytes=-1 \\\\      # 允许存储的所有消息的大小，-1为无限制
      --max-age=1y \\\\        # 消息存储的最长时间，超时会自动删除, y,s,m,h
      --max-msg-size=-1 \\\\   # 单条消息允许的大小，-1为无限制
      --dupe-window=2m0s \\\\  # 根据Msg-Id的header头信息判断唯一消息的时间
      --allow-rollup \\\\      # 是否允许通过header“卷起”消息, --no-allow-rollup
      --deny-delete \\\\       # 是否允许删除消息，--no-deny-delete)
      --deny-purge \\\\        # 是否允许清空stream消息, --no-deny-purge
      --replicas=1 \\\\        # 消息副本的数量

# 持久化的方式
limits 消息限制
interest 存在消费者就存储
workq 队列形式存储

```

## Consumer

```sql
nats consumer add "TEST" \\\\ # Stream名称
									"A" \\\\    # Consumer名称)
			--filter "test.*" \\\\  # 过滤主题
			--deliver all \\\\      # 消息投递策略: all/new/last/subject/1h/msg sequence
			--ack explicit \\\\     # 消息确认方式: none/all/explicit
			--replay=instant \\\\   # 消息回放机制：instant/original
			--max-deliver=-1 \\\\   # 消息投递的最大次数，-1表示没有正确的ack就不停止投递消息
			--max-pending=0 \\\\    # 最多允许存在多少条正在投递但未投递成功的消息，如果到达这个数，将不再投递新消息
			--headers-only \\\\     # 是否只投递Header消息
			--target "beijing.course.created" # 在“推送”模式下，消息实时发送到目标NATS主题，可以作为普通消息订阅beijing.course.created主题，pull类型的consumer该值为空

# 消息投递策略
all 默认配置，consumer需要处理从头开始的所有消息
last 从stream中的最后一条消息开始
subject 从stream中每个主题的最后一条消息开始 （第一次）
new 只处理consumer创建后的新消息，历史消息不处理
msg sequence 指定sequence开始处理
1h 指定时间开始处理

# 消息确认方式
none 不需要确认
all 确定的消息之前的消息都确定
explicit 每条消息都需要确定

# 消息回放机制
instant,立即消费，我创建完这个consumer就可以立即消费所有消息
original,按照stream收到消息的这个频率来推送消息给我

```

## 主题（Subject）命名以及通配符的使用

### 命令

推荐字符: a to z, A to Z, 0 to 9 (大小写敏感，不能有空格). 特殊字符: 区分层次结构用“.”，两个通配符: “`*`”，“`>`”

- : 表示同级的匹配 `>`: 表示所有子级的匹配