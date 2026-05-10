---
title: 一款PostgreSQL WAL日志解析工具: wal2json - 文章详情
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
---

## 一款PostgreSQL WAL日志解析工具: wal2json - 文章详情

URL: https://z.itpub.net/article/detail/A7B1C976613E89A62B17B5B689E7FF75

**译者****简介**

**钟硕**， 现供职于迪卡侬，PostgreSQL & Oracle DBA

Debezium 属于红帽开源项目， wal2json作为其组成部分可以提供基于PG库级别的DML日志挖掘工作。

Debezium针对几类主流数据库的连接器：

https://debezium.io/docs/connectors/

Wal2json安装页面**：**

https://debezium.io/docs/install/postgres-plugins/

Debezium 目地在于提供一个分布式的平台，将数据库日志中的事件记录转化为事件流，使得外部应用能够对数据库中的行级操作做出快速响应。Debezium 可以建立在Apache kafka的上层，为kafka connect提供可兼容的连接器用于监控和管理特定的数据库。

**wal2json****工具的安装**

自从PostgreSQL 9.4 支持logical级别的WAL后，PostgreSQL可以通过逻辑解码的方式（基于行或语句的logical replication）来解读WAL中的内容。

Logical Decoding Output Plugins

https://www.postgresql.org/docs/9.4/logicaldecoding-output-plugin.html

WAL_LEVEL支持的四个级别

https://www.postgresql.org/docs/9.4/runtime-config-wal.html

通过slot在从节点解码WAL中的数据变更流进行重演实现数据库的逻辑复制。

安装：

export PATH="$PATH:/usr/pgsql-9.5/bin"

注意：这里一定要确认系统环境中找到正确版本的pg_config（如果安装过多个版本的PostgreSQL）

```
git clone https://github.com/eulerto/wal2json -b master --single-branchcd wal2jsonmake && make install 
```

正确安装后的输出：

`Cloning into 'wal2json'...``remote: Enumerating objects: 47, done.``remote: Counting objects: 100% (47/47), done.``remote: Compressing objects: 100% (24/24), done.``remote: Total 496 (delta 26), reused 38 (delta 23), pack-reused 449``Receiving objects: 100% (496/496), 204.39 KiB |  bytes/s, done.``Resolving deltas: 100% (345/345), done.``gcc -Wall -Wmissing-proto* -Wpointer-arith -Wdeclaration-after-statement -Wendif-labels -Wmissing-format-attribute -Wformat-security -fno-strict-aliasing -fwrapv -fexcess-precision=standard -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC -I. -I./ -I/usr/pgsql-9.5/include/server -I/usr/pgsql-9.5/include/internal -D_GNU_SOURCE -I/usr/include/libxml2  -I/usr/include  -c -o wal2json.o wal2json.c``gcc -Wall -Wmissing-proto* -Wpointer-arith -Wdeclaration-after-statement -Wendif-labels -Wmissing-format-attribute -Wformat-security -fno-strict-aliasing -fwrapv -fexcess-precision=standard -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC -L/usr/pgsql-9.5/lib -Wl,--as-needed  -L/usr/lib64 -Wl,--as-needed -Wl,-rpath,'/usr/pgsql-9.5/lib',--enable-new-dtags  -shared -o wal2json.so wal2json.o``/usr/bin/mkdir -p '/usr/pgsql-9.5/lib'``/usr/bin/install -c -m 755  wal2json.so '/usr/pgsql-9.5/lib/'`

PostgreSQL服务端配置

`shared_preload_libraries = 'wal2json'``wal_level = logical``max_wal_senders = 4` `max_replication_slots = 4`

创建具有Replication和Login授权的用户

`CREATE ROLE <name> REPLICATION LOGIN;`

修改pg_hba.conf，使该用户可以远程或本地访问数据库

`############ REPLICATION ##############``local   replication     <name>                              trust``host    replication     <name>    127.0.0.1/32     trust host    replication     <name>    ::1/128              trust`

建立测试环境

`CREATE DATABASE test;``CREATE TABLE test_table (` `id char(10) NOT NULL,` `code        char(10),` `PRIMARY KEY (id)``);`

另开一个会话窗口

`pg_recvlogical -d test --slot test_slot --create-slot -P wal2json``pg_recvlogical -d test --slot test_slot --start -o pretty-print=1 -f -`

进行一些基本的DML操作

`test=# INSERT INTO test_table (id, code) VALUES('id1', 'code1');``INSERT 0 1``test=# update test_table set code='code2' where id='id1';``UPDATE 1``test=# delete from test_table where id='id1';``DELETE 1`

对应DML的输出：

**INSERT**

`{` `"change": [` `{` `"kind": "insert",` `"schema": "mdmv2",` `"table": "test_table",` `"columnnames": ["id", "code"],` `"column*": ["character(10)", "character(10)"],` `"columnvalues": ["id1       ", "code1     "]` `}` `]``}`

**UPDATE**

`{` `"change": [` `{` `"kind": "update",` `"schema": "mdmv2",` `"table": "test_table",` `"columnnames": ["id", "code"],` `"column*": ["character(10)", "character(10)"],` `"columnvalues": ["id1       ", "code2     "],` `"oldkeys": {` `"keynames": ["id"],` `"key*": ["character(10)"],` `"keyvalues": ["id1       "]` `}` `}` `]``}`

**DELETE**

`{` `"change": [` `{` `"kind": "delete",` `"schema": "mdmv2",` `"table": "test_table",` `"oldkeys": {` `"keynames": ["id"],` `"key*": ["character(10)"],` `"keyvalues": ["id1       "]` `}` `}` `]``}`

也可以用PostgreSQL提供的REPLICA IDENTITY来决定对表UPDATE和DELETE操作时，logical日志输出信息的详细程度。

DEFAULT  逻辑日志中包含表中主键列被UPDATE或DELETE的前值的信息。

NOTHING 逻辑日志中不包含表中任何UPDATE或DELETE变更的信息。

FULL 逻辑日志中包含表中被UPDATE或DELETE列的前值的整行信息。

USING INDEX <index_name>仅包含指定索引中所有列的前值信息。

ALTER TABLE test_table REPLICA IDENTITY USING INDEX test_table_pkey；

`{` `"change": [` `{` `"kind": "update",` `"schema": "mdmv2",` `"table": "test_table",` `"columnnames": ["id", "code"],` `"column*": ["character(10)", "character(10)"],` `"columnvalues": ["id1       ", "code2     "],` `"oldkeys": {` `"keynames": ["id"],` `"key*": ["character(10)"],` `"keyvalues": ["id1       "]` `}` `}` `]``}`

注意：

wal2json 插件无法很好的处理引号标识符

wal2json 插件无法输出没有主键的表的事件信息

wal2json 插件不支持特殊浮点类型的值（Nan 或 infinity）

**参考**

https://debezium.io/docs/install/postgres-plugins/