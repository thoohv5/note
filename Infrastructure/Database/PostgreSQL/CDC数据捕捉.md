---
title: PostgreSQL 变化数据捕捉（CDC）_pgoutput-CSDN博客
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
---

## PostgreSQL 变化数据捕捉（CDC）_pgoutput-CSDN博客

URL: https://blog.csdn.net/sunny05296/article/details/128960676

[PostgreSQL](https://so.csdn.net/so/search?q=PostgreSQL&spm=1001.2101.3001.7020) 变化数据捕捉（CDC）

基于[CDC](https://so.csdn.net/so/search?q=CDC&spm=1001.2101.3001.7020)（变更数据捕捉）的增量数据集成总体步骤：

1.捕获源数据库中的更改数据

2.将变更的数据转换为您的消费者可以接受的格式

3.将数据发布到消费者或目标数据库

PostgreSQL支持触发器（trigger）和预写日志（WAL）两种CDC机制。

如果您希望 Postgres 数据更改发生时逐行流式传输，则需要逻辑解码或 Postgres 逻辑复制功能。

使用 Postgres 逻辑解码

逻辑解码是 PostgreSQL 的基于日志的 CDC（逻辑复制）的正式名称。逻辑解码使用 PostgreSQL 预写日志的内容来存储数据库中发生的所有活动。

step1：修改PostgreSQL数据库配置postgresql.conf文件

wal_level = logical

max_replication_slots = 10

max_wal_senders = 20

wal_level ：设置为 logical，允许 WAL 日志记录逻辑解码所需的信息。

max_replication_slots ：确保 max_replication_slots >= 使用 WAL 的 PostgreSQL 连接器的数量加上您的数据库使用的其他复制槽的数量。

max_wal_senders ：指定 WAL 的最大并发连接数的参数，确保 max_wal_senders 至少是逻辑复制槽数的两倍。例如，如果您的数据库总共使用 10 个复制槽，则该 max_wal_senders 值必须为 20 或更大。

配置修改以后，需要重复 PostgreSQL 服务生效配置。

step2：为需要同步的数据库（db）创建逻辑复制插槽（replication slot）

关于复制 SQL 函数更多详情，可以参考官方文档（9.26.系统管理函数）介绍： [http://www.postgres.cn/docs/12/functions-admin.html](http://www.postgres.cn/docs/12/functions-admin.html)

函数：pg_create_logical_replication_slot(slot_name name, plugin name)

返回类型：(slot_name name, lsn pg_lsn)

说明：使用输出插件plugin创建一个名为 slot_name的新逻辑（解码）复制槽。

创建时需要指定逻辑复制插槽名称和输出插件：

SELECT pg_create_logical_replication_slot('replication_slot01', 'test_decoding'); -- 使用 test_decoding 输出插件

SELECT pg_create_logical_replication_slot('replication_slot01', 'pgoutput'); -- 使用 pgoutput 输出插件

注意权限：

如果用户没有权限可能会报错：ERROR: must be superuser or replication role to use replication slots

授权：

\c - postgres

ALTER ROLE test REPLICATION; --流复制权限

\du

验证插槽是否创建成功：

SELECT * FROM pg_replication_slots;

testdb01=> SELECT * FROM pg_replication_slots;

slot_name | plugin | slot_type | datoid | database | temporary | active | active_pid | xmin | catalog_xmin | restart_lsn | confirmed_flush_lsn

--------------------+---------------+-----------+--------+----------+-----------+--------+------------+------+--------------+-------------+---------------------

replication_slot01 | test_decoding | logical | 16437 | testdb01 | f | f | | | 585 | 0/1807EA8 | 0/1807EE0

replication_slot02 | pgoutput | logical | 16437 | testdb01 | f | f | | | 585 | 0/1809CD8 | 0/1809D10

(2 rows)

testdb01=>

注意：

1）逻辑复制插槽名称：每个逻辑复制插槽都有一个名称，创建时需要指定名称，可以包含小写字母、数字和下划线

2）输出插件：创建逻辑复制插槽时需要指定输出插件，输出插件有：test_decoding、pgoutput、wal2json等

test_decoding 输出插件 ：PostgreSQL 9.4+原生附带了 test_decoding 输出插件（$PG_HOME/lib 目录下对应有 test_decoding.so），如果您的消费者支持 test_decoding ，则可以使用 test_decoding 输出插件。

pgoutput 输出插件 ：PostgreSQL 10+原生附带了 pgoutput 输出插件（$PG_HOME/lib 目录下对应有 pgoutput.so），如果您的消费者支持 pgoutput ，则可以使用 pgoutput 输出插件。pgoutput插件输出的是二进制的数据

wal2json 输出插件 ：wal2json 是另一个流行的逻辑解码输出插件，PostgreSQL原生不携带、需要数据库服务单独安装插件才能使用。wal2json输出的是json格式的数据

step3：为数据库中的所有表或指定表创建一个发布。如果以指定表方式创建发布则后面可以在发布中管理（添加、删除）表

帮助说明：\h create publication

DROP PUBLICATION IF EXISTS pub01;

CREATE PUBLICATION pub01 FOR TABLE test01;

或者

DROP PUBLICATION IF EXISTS pub01;

CREATE PUBLICATION pub01 FOR TABLE test01, test02;

或者

DROP PUBLICATION IF EXISTS pub01;

CREATE PUBLICATION pub01 FOR ALL TABLES;

查看创建发布的结果：

testdb01=> select * from pg_publication;

oid | pubname | pubowner | puballtables | pubinsert | pubupdate | pubdelete | pubtruncate

-------+---------+----------+--------------+-----------+-----------+-----------+-------------

24629 | pub01 | 16436 | f | t | t | t | t

(1 row)

也可以执行查看：

testdb01=> \dRp

List of publications

Name | Owner | All tables | Inserts | Updates | Deletes | Truncates

-------+-------+------------+---------+---------+---------+-----------

pub01 | test | f | t | t | t | t

(1 row)

如果想要看到发布关联的表：

testdb01=> select * from pg_publication_tables;

pubname | schemaname | tablename

---------+------------+-----------

pub01 | public | test01

pub01 | public | test02

(2 rows)

testdb01=> \dRp+

Publication pub01

Owner | All tables | Inserts | Updates | Deletes | Truncates

-------+------------+---------+---------+---------+-----------

test | f | t | t | t | t

Tables:

"public.test01"

"public.test02"

创建发布以后，后续可以修改发布来添加删除表：

testdb01=> ALTER PUBLICATION pub01 DROP TABLE test02;

testdb01=> ALTER PUBLICATION pub01 ADD TABLE test02;

创建发布时，还可以选择在发布中包含哪些操作。例如，下面仅创建table01表 & 仅包含INSERT和UPDATE的发布：

CREATE PUBLICATION pub01 FOR TABLE table01 WITH (publish = 'INSERT, UPDATE');

注意：

非超级用户创建发布只能指定表创建、不能 FOR ALL TABLES 创建，否则报错：ERROR: must be superuser to create FOR ALL TABLES publication

FOR ALL TABLES 只允许超级用户创建发布，或者创建后由超级用户修改已创建的发布的配置开启foralltables。

testdb01=> \c - postgres

testdb01=# update pg_publication set puballtables=true where pubname is not null; -- 设置puballtables开关

testdb01=# select * from pg_publication;

oid | pubname | pubowner | puballtables | pubinsert | pubupdate | pubdelete | pubtruncate

-------+---------+----------+--------------+-----------+-----------+-----------+-------------

24629 | pub01 | 16436 | t | t | t | t | t

(1 row)

step4：验证指定的表是否在发布中

testdb01=> SELECT * FROM pg_publication_tables WHERE pubname='pub01';

注意：创建发布不会开始复制。它只为未来的订阅者定义一个分组和过滤逻辑。

step5：查看逻辑复制效果：

获取结果的相关命令：

pg_logical_slot_get_changes : 查询并删除数据。仅在第一次返回结果，多次调用可能会返回空结果集，这意味着当get命令执行时，结果会被提供和删除，这增强了我们编写使用这些事件创建表副本的逻辑的能力。

函数：pg_logical_slot_get_changes(slot_name name, upto_lsn pg_lsn, upto_nchanges int, VARIADIC options text[])

返回类型：(lsn pg_lsn, xid xid, data text)

说明：返回槽slot_name中的改变，从上一次已经被消费的点开始返回。 如果upto_lsn和upto_nchanges为 NULL，逻辑解码将一 直继续到 WAL 的末尾。如果upto_lsn为非 NULL，解码将只包括那些在指 定 LSN 之前提交的事务。如果upto_nchanges为非 NULL， 解码将在其产生的行数超过指定值后停止。不过要注意， 被返回的实际行数可能更大，因为对这个限制的检查只会在增加了解码每个新的提交事务产生 的行之后进行。

pg_logical_slot_get_binary_changes：变化数据以bytea返回。

pg_logical_slot_peek_changes : 只查询不删数据。多次调用每次都会返回相同的结果。是另一个 PostgreSQL 命令，用于在不使用 WAL 条目的情况下查看更改。

函数：pg_logical_slot_peek_changes(slot_name name, upto_lsn pg_lsn, upto_nchanges int, VARIADIC options text[])

返回类型：(lsn text, xid xid, data text)

说明：行为就像pg_logical_slot_get_changes()函数， 不过改变不会被消费， 即在未来的调用中还会返回这些改变。

pg_logical_slot_peek_binary_changes：变化数据以bytea返回。

关于复制 SQL 函数更多详情，可以参考官方文档（9.26.系统管理函数）介绍： [http://www.postgres.cn/docs/12/functions-admin.html](http://www.postgres.cn/docs/12/functions-admin.html)

测试表中I/D/U变更数据操作，然后查看逻辑复制效果：

testdb01=> SELECT * FROM pg_logical_slot_peek_changes('replication_slot01', NULL, NULL); -- 只查询不删数据

......

testdb01=> SELECT * FROM pg_logical_slot_get_changes('replication_slot01', NULL, NULL); -- 查询并删除数据

lsn | xid | data

-----------+-----+------------------------------------------------------------------------------------------

0/180A050 | 586 | BEGIN 586

0/180A050 | 586 | table public.test01: INSERT: id[bigint]:1 info[character varying]:'aa' cnt[integer]:123

0/180A160 | 586 | table public.test01: INSERT: id[bigint]:2 info[character varying]:'bb' cnt[integer]:456

0/180A1E8 | 586 | table public.test01: INSERT: id[bigint]:3 info[character varying]:'cc' cnt[integer]:55

0/180A2A0 | 586 | COMMIT 586

0/180A2A0 | 587 | BEGIN 587

0/180A2A0 | 587 | table public.test01: INSERT: id[bigint]:4 info[character varying]:'uuu' cnt[integer]:66

0/180A358 | 587 | COMMIT 587

0/180A358 | 588 | BEGIN 588

0/180A358 | 588 | table public.test01: DELETE: id[bigint]:2

0/180A3D0 | 588 | COMMIT 588

0/180A3D0 | 589 | BEGIN 589

0/180A3D0 | 589 | table public.test01: UPDATE: id[bigint]:3 info[character varying]:'xxxx' cnt[integer]:55

0/180A458 | 589 | COMMIT 589

(14 rows)

testdb01=> SELECT * FROM pg_logical_slot_get_changes('replication_slot01', NULL, NULL); -- 查询并删除数据

lsn | xid | data

-----+-----+------

(0 rows)

testdb01=>

pgoutput输出插件的结果查看，则执行：

testdb01=> SELECT * FROM pg_logical_slot_peek_binary_changes('replication_slot02', null, null, 'proto_version', '1', 'publication_names', 'pub01'); -- 查询但不删除数据

......

testdb01=> SELECT * FROM pg_logical_slot_get_binary_changes('replication_slot02', null, null, 'proto_version', '1', 'publication_names', 'pub01'); -- 查询并删除数据

lsn | xid | data

-----------+-----+------------------------------------------------------------------------------------------------------------------------------

0/1809DF8 | 585 | \x420000000001809fd0000296c5f556e57d00000249

0/1809DF8 | 585 | \x52000060417075626c696300746573743031006400030169640000000014ffffffff00696e666f00000004130000006800636e740000000017ffffffff

0/1809DF8 | 585 | \x44000060414b00037400000001316e6e

0/1809F40 | 585 | \x44000060414b00037400000001346e6e

0/1809F88 | 585 | \x44000060414b00037400000001336e6e

0/180A000 | 585 | \x43000000000001809fd0000000000180a000000296c5f556e57d

0/180A050 | 586 | \x42000000000180a270000296c5f7bb75300000024a

0/180A050 | 586 | \x49000060414e0003740000000131740000000261617400000003313233

0/180A160 | 586 | \x49000060414e0003740000000132740000000262627400000003343536

0/180A1E8 | 586 | \x49000060414e00037400000001337400000002636374000000023535

0/180A2A0 | 586 | \x4300000000000180a270000000000180a2a0000296c5f7bb7530

0/180A2A0 | 587 | \x42000000000180a328000296c5f7d8abe90000024b

0/180A2A0 | 587 | \x49000060414e0003740000000134740000000375757574000000023636

0/180A358 | 587 | \x4300000000000180a328000000000180a358000296c5f7d8abe9

0/180A358 | 588 | \x42000000000180a3a0000296c5f80ae6ca0000024c

0/180A358 | 588 | \x44000060414b00037400000001326e6e

0/180A3D0 | 588 | \x4300000000000180a3a0000000000180a3d0000296c5f80ae6ca

0/180A3D0 | 589 | \x42000000000180a428000296c5f826073e0000024d

0/180A3D0 | 589 | \x55000060414e000374000000013374000000047878787874000000023535

0/180A458 | 589 | \x4300000000000180a428000000000180a458000296c5f826073e

(20 rows)

testdb01=> SELECT * FROM pg_replication_slots;

slot_name | plugin | slot_type | datoid | database | temporary | active | active_pid | xmin | catalog_xmin | restart_lsn | confirmed_flush_lsn

--------------------+---------------+-----------+--------+----------+-----------+--------+------------+------+--------------+-------------+---------------------

replication_slot01 | test_decoding | logical | 16437 | testdb01 | f | f | | | 590 | 0/180A540 | 0/180A578

replication_slot02 | pgoutput | logical | 16437 | testdb01 | f | f | | | 590 | 0/180A540 | 0/180A578

(2 rows)

相关说明：

1）from pg_replication_slots 视图说明：

pg_replication_slots 视图提供了数据库集群上当前存在的所有复制插槽（replication slots）的列表及其当前状态，参数如下：

Name Type ReferencesDescription

-------- -------- ------------------------

slot_name name 复制插槽名称，为集群范围内的唯一标识符。

plugin name 正在使用的包含逻辑槽输出插件的共享对象的基本名称，即逻辑解码输出插件名（例如：test_decoding、pgoutput、wal2json），对于物理插槽则为空。

slot_type text 插槽类型：logical 代表逻辑插槽，physical 代表物理插槽。

datoid oid 该插槽所关联的数据库的 OID，只有逻辑插槽才具有关联的数据库，物理插槽则没有、即为空。

database text 该插槽所关联的数据库的名称，只有逻辑插槽才具有关联的数据库，物理插槽则没有、即为空。

temporary boolean PG 10开始支持该参数。是否为临时插槽。创建插槽时，如果声明此 replication slot 为临时插槽，则不会存在磁盘、会在失败后删除。

active boolean 如果此插槽当前正在使用，则为真。

active_pid integer 对应的流复制进程ID。如果当前正在使用插槽（活跃），则使用此插槽的会话的进程的 PID。如果不活动则为空。

xmin xid 此插槽需要数据库保留的最早事务。VACUUM 无法删除任何后来的事务删除的元组。

catalog_xmin xid 这个插槽要需要数据库保留的影响系统目录的最旧事务。VACUUM不能移除被其后续事务删除的目录元组。

restart_lsn pg_lsn 可能仍被这个插槽的消费者要求的最旧的 WAL 地址（LSN），并且因此不会在检查点期间自动被移除。

confirmed_flush_lsn pg_lsn 代表逻辑插槽的消费者已经确认接收数据到什么位置的地址（LSN）。比这个地址更旧的数据已经不再可用。对于物理槽这里为空。

wal_status text PG 13开始支持该参数。此插槽声称的 WAL 文件的可用性。

可能的值为:

reserved 意味着声称的文件包含 max_wal_size。

extended 意味着 max_wal_size 已超出，但文件仍保留，通过复制插槽或 wal_keep_size。

unreserved 意味着该插槽不再保留所需的 WAL 文件，并且将在下一个检查点删除其中一些文件。 此状态可以返回到 reserved 或 extended。

lost 意味着某些需要的 WAL 文件已被删除，并且此插槽不再可用。

最后两种状态仅在 max_slot_wal_keep_size 为非负值时才看到。 如果 restart_lsn 为 NULL，则此字段为空。

safe_wal_size int8 PG 13开始支持该参数。可写入 WAL 的字节数，以便此插槽不会处于"丢失"状态的危险中。 对丢失插槽它是NULL，以及如果max_slot_wal_keep_size是-1。

two_phase boolean PG 14开始支持该参数。如果该插槽为解码准备事务所启用则为真。物理插槽总是为假。

注意：不同版本会有差异，更多版本详情参考官方说明参考：[http://www.postgres.cn/docs/10/view-pg-replication-slots.html](http://www.postgres.cn/docs/10/view-pg-replication-slots.html)

2）PG logical replication plug-in ("pgoutput") 输出的消息格式详情：可以参考PG官方说明：[https://www.postgresql.org/docs/10/protocol-logicalrep-message-formats.html](https://www.postgresql.org/docs/10/protocol-logicalrep-message-formats.html)

step6：销毁逻辑复制插槽

SELECT pg_drop_replication_slot('replication_slot01');

使用复制插槽的注意事项：

1）每个插槽只有一个输出插件（创建插槽时由您来选择使用哪个）。

2）每个插槽仅提供来自一个数据库的更改。

3）一个数据库可以有多个插槽。

4）每个数据更改通常在每个插槽中发出一次。

5）但是当 Postgres 实例重新启动时，插槽可能会重新发出更改。消费者必须处理这种情况。

6）未使用的插槽对 Postgres 实例的可用性构成威胁。Postgres 将为这些未使用的更改保存所有 WAL 文件。这可能导致存储溢出。

关于 PostgreSQL WAL 消费者：

可以获取 Postgres 逻辑解码流的任何应用程序都是 PostgreSQL WAL 消费者。

pg_recvlogical：

pg_recvlogical 是一个 PostgreSQL 应用程序，他是 PostgreSQL 原生的逻辑解码工具，它可以管理槽并使用槽中的流。它包含在 Postgres 发行版中，因此它可能已经随 PostgreSQL 一起安装，在 $PG_HOME/bin 目录下。

pg_recvlogical 使用默认的 test_decoding 逻辑解码插件，pg安装完后会在pg安装目录的lib目录下创建test_decoding链接库文件。

pg_recvlogical 使用介绍：

创建逻辑复制插槽：

$ pg_recvlogical --create-slot -S replication_slot01 -d testdb01

启动复制槽解码（启动后会实时的将日志解码到制定的文件中，也可以不启动、等需要解码时再启动解码）

$ pg_recvlogical --start -S replication_slot01 -d testdb01 -f replication_slot01_decoding.log &

查看解码结果：

$ cat replication_slot01_decoding.log

查看数据库lsn：

postgres=# select pg_current_wal_lsn();

使用pg_recvlogical进行日志区间解码：

$ pg_recvlogical --start -S replication_slot01 -d testdb01 -I 4C/180B020 -E 4C/180B1EE -f ret.log

其他常用相关查询命令：

WAL日志及LSN查询：

1.查看视图 pg_replication_slots 信息：

testdb01=> select * from pg_replication_slots ;

slot_name | plugin | slot_type | datoid | database | temporary | active | active_pid | xmin | catalog_xmin | restart_lsn | confirmed_flush_lsn

--------------------+---------------+-----------+--------+----------+-----------+--------+------------+------+--------------+-------------+---------------------

replication_slot01 | test_decoding | logical | 16437 | testdb01 | f | f | | | 590 | 0/180A540 | 0/180A578

replication_slot02 | pgoutput | logical | 16437 | testdb01 | f | f | | | 594 | 0/180C270 | 0/29AA7B0

(2 rows)

2.pg_controldata 查看控制信息：

$ pg_controldata

pg_control version number: 1201

Catalog version number: 201909212

Database system identifier: 7038444130751219893

Database cluster state: in production

pg_control last modified: Fri 10 Feb 2023 02:29:59 PM CST

Latest checkpoint location: 0/29AA700

Latest checkpoint's REDO location: 0/29AA6C8

Latest checkpoint's REDO WAL file: 000000010000000000000002

Latest checkpoint's TimeLineID: 1

Latest checkpoint's PrevTimeLineID: 1

Latest checkpoint's full_page_writes: on

Latest checkpoint's NextXID: 0:626

Latest checkpoint's NextOID: 35373

......

3.查询数据库LSN等信息：

-- PG10以前的版本

select pg_current_xlog_location(),pg_xlogfile_name(pg_current_xlog_location()),pg_xlogfile_name_offset(pg_current_xlog_location());

其中：

pg_current_xlog_location()：获得当前wal日志写入位置。

pg_xlogfile_name():转换wal日志位置为文件名。

pg_xlogfile_name_offset():返回转换后的wal日志文件名和偏移量。

-- PG10及以后版本

select pg_current_wal_lsn(),pg_walfile_name(pg_current_wal_lsn()),pg_walfile_name_offset(pg_current_wal_lsn());

testdb01=> select pg_current_wal_lsn(),pg_walfile_name(pg_current_wal_lsn()),pg_walfile_name_offset(pg_current_wal_lsn());

pg_current_wal_lsn | pg_walfile_name | pg_walfile_name_offset

--------------------+--------------------------+-------------------------------------

0/29AA7B0 | 000000010000000000000002 | (000000010000000000000002,10135472)

(1 row)

其中：

pg_current_wal_lsn()： 获得当前wal日志写入位置。

pg_walfile_name()： 转换wal日志位置为文件名。

pg_walfile_name_offset()：返回转换后的wal日志文件名和偏移量。

说明

LSN:0/29AA7B0

0：代表wal文件的第二部分

2：代表wal文件的最后两位02

9AA7B0：代表对应日志文件内的偏移量

4.查看pg_wal目录下的日志文件：

$ ls -l $PGDATA/pg_wal

total 32768

-rw-------. 1 postgres postgres 16777216 Feb 6 09:20 000000010000000000000001

-rw-------. 1 postgres postgres 16777216 Feb 10 14:30 000000010000000000000002

drwx------. 2 postgres postgres 6 Dec 6 2021 archive_status

wal文件由三部分组成每个4字节（8个16进制字符）总共12字节24个16进制字符。第一部分（4字节）代表时间线（TimeLineID）、第二部分代表LSN高32位、第三部分代表LSN低32位（LSN低32位的最高两位代表该日志文件的LSN起始编号）。

000000010000000000000002 日志文件名的格式如下：

00000001：第一部分（4字节）代表数据库运行的时间轴，如果恢复过数据库（主备切换）这个值会增大；

00000000：第二部分、LSN高32位；

00000002：第三部分、LSN低32位。其中最高两位为0代表LSN该日志文件的LSN其实编号为0。

根据时间线 00000001 和 0/29AA7B0 可以推算出日志文件为：000000010000000000000002，对位的文件内的偏移为：0x9AA7B0 = 10135472

5.查询当前的最新LSN和WAL文件：

testdb01=> select * from pg_control_checkpoint();

checkpoint_lsn | redo_lsn | redo_wal_file | timeline_id | prev_timeline_id | full_page_writes | next_xid | next_oid | next_multixact_id | next_multi_offset |

oldest_xid | oldest_xid_dbid | oldest_active_xid | oldest_multi_xid | oldest_multi_dbid | oldest_commit_ts_xid | newest_commit_ts_xid | checkpoint_time

----------------+-----------+--------------------------+-------------+------------------+------------------+----------+----------+-------------------+-------------------+-

-----------+-----------------+-------------------+------------------+-------------------+----------------------+----------------------+------------------------

0/29AA700 | 0/29AA6C8 | 000000010000000000000002 | 1 | 1 | t | 0:626 | 35373 | 1 | 0 |

479 | 1 | 626 | 1 | 1 | 0 | 0 | 2023-02-10 14:29:56+08

(1 row)

testdb01=>