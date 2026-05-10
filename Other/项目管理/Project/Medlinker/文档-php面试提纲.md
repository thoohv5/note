---
title: php面试提纲
date: 2026-04-07
tags: [其他, 项目管理]
type: guide
status: complete
---

## php面试提纲

**项目部分**
1. 介绍一下过往最熟悉的项目架构
2. 介绍一下在项目中担任的职责
3. 流程图和数据库的设计
4. 缓存的设计&使用，缓存和DB的一致性解决方案有哪些，如何保证缓存不被穿透
5. 项目中遇到过什么重大问题，如何解决，如何排查问题
6. 项目中使用的能力&经验，如何去优化&重构
**Linux**
1. linux使用过哪些发行版
2. linux中的进程有哪些挂起方式
3. linux有几种日志查看方式，常用的参数有哪些
4. 如果一个日志大小超过内存大小，如何查看
5. bash词频统计，如何在一个文件中，筛选每个英文单词出现的频率，并逆序排列
6. 如何过滤日志
7. linux如何查看哪个端口被什么进程占用
8. linux下如何抓包
9. 进程线程问题
**Nginx**
1. nginx用过哪些版本，版本差异有哪些
2. nginx高并发的原因
3. nginx如何做负载均衡
4. nginx插件用过哪些
5. 502出现的原因
**DB**
1. mysql用过哪些版本，版本差异有哪些
2. 一条dml语句，从客户端到落盘经历了哪些，主要考察双写
3. varchar和char的区别
4. 为什么mysql是b+Tree，mongo是bTree，如果bTree每个索引的value加上下一个数的指针，可不可以取代mysql的B+ tree?
5. 慢sql排查技巧
6. 如何查看sql的索引使用情况
7. 联合索引的使用
**Redis**
1. redis和memcache的差异
2. redis的多路复用与单线程
3. redis高并发的原因
4. redis的大key操作
5. redis的数据结构
6. redis持久化
**Php**
1. php5与7版本差异
2. php使用过哪些框架，框架之间的不同
3. swoole了解的话，介绍一下
4. php的设计模式以及魔术方法
5. fastcgi&fpm介绍
6. php在linux下，哪些需要注意大小写的问题
7. 抽象类和接口介绍
**Mq**
1. 为什么使用mq
2. kafka高吞吐的原因
3. qos的差别
4. 如何增加producer的速度
5. 什么是consumer_group？
6. kafka的消息有序性
**网络部分**
1. XSS攻击
2. POST与GET区别
3. http状态码介绍
4. 一个网页显示在浏览器经历了什么
5. http&https的区别
6. 为什么tcp传文件会越来越快
7. http3.0了解的话，介绍下
8. udp广播的原理