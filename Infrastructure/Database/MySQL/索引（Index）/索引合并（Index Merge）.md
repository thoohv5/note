---
title: 索引合并（Index Merge）
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: reference
status: complete
---

## 索引合并（Index Merge）

## 概述

MySQL5.0之前，一个表一次只能使用一个索引，无法同时使用多个索引分别进行条件扫描。但是从5.1开始，引入了 index merge 优化技术，对同一个表可以使用多个索引分别进行条件扫描。

对多个索引分别进行条件扫描，然后将它们各自的结果进行合并(intersect/union)

索引合并是通过多个range类型的扫描并且合并它们的结果集来检索行的。仅合并来自单个表的索引扫描，而不是跨多个表的索引扫描。合并会产生底层扫描的三种形式：unions（合并）、intersections（交集）、unions-of-intersections（先取交集再合并）

## 附录

[digdeep](https://www.cnblogs.com/digdeep/p/4975977.html)