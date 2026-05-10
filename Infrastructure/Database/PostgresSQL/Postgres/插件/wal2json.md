---
title: wal2json
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## wal2json

## wal2json插件

wal2json是逻辑解码插件，使用该插件可以访问INSERT和UPDATE生成的元组，解析WAL中的内容。wal2json插件会在每个事务中生成一个JSON对象。JSON对象总提供了所有新/旧元组，额外选项还可以包括事务时间戳，限定架构，数据类型，事务ID等属性。

参数

| 选项 | 描述 |
| --- | --- |
| include-xids | 添加xid 信息，默认false |
| include-timestamp | 添加 timestamp信息，默认false |
| include-schemas | 添加schemas信息，默认true |
| include-types | 添加type信息，默认true |
| include-typmod | 添加 typmod 信息，默认true |
| include-type-oids | 添加 type oids信息，默认false |
| include-domain-data-type | 将域名替换成数据类型名，默认false |
| include-column-positions | 添加 column position 信息，默认false |
| include-origin | 添加origin信息，默认false |
| include-not-null | 添加not nul1信息，默认false |
| include-default | 添加默认值表达式，默认为false |
| include-pk | 添加主键信息，默认为false |
| pretty-print | 更好的输出ison结构，默认为false |
| include-lsn | 添加nextlsn信息，默认为false |
| write-in-chunks | 在每次更改后写入，而不是在每次更改集之后写入。只在format-version为1的时候有用，默认为 false |
| filter-origins | 排除指定origin的更改，默认为空不过滤，多个 origin用逗号分隔 |
| filter-tables | 排除指定table的更改，默认为空不过滤，多个table用逗号分隔 |
| add_tables | 仅包含指定table的更改，默认值是所有的表，与filter-tables值具有相同的规则 |
| format-version | 格式化方式，可选值为1和2，默认为1 |
| actions | 解析的actions，默认是所有的action（insert，update，delete） |

## 附录

[https://github.com/eulerto/wal2json](https://github.com/eulerto/wal2json)

[https://github.com/eulerto/wal2json/blob/master/expected/message.out](https://github.com/eulerto/wal2json/blob/master/expected/message.out)