---
title: DB_ROW_ID
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: incomplete
---

# DB_ROW_ID

占用空间: 6字节
存在条件: 如果用户没有定义主键，则选取一个Unique键作为主键，如果表中连Unique键都没有定义的话。
注意：unique 健 都是 not null的
描述: 行ID，唯一标识一条记录
是否必须: 否