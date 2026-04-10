---
title: OpenCensus
date: 2026-04-07
tags: [微服务, 服务治理]
type: reference
status: complete
---

# OpenCensus

# 概述

OpenSensus是允许你采集应用程序Metrics和分布式Traces，并且支持各种语言的库，比如：Go, Java, C#, Node.js, C++, [Ruby](https://so.csdn.net/so/search?q=Ruby&spm=1001.2101.3001.7020), Erlang/Elixir, Python, Scala and PHP。支持将数据实时传输到后端。开发人员或管理员可以分析这些数据，以了解应用程序的运行状况或调试问题。

读到这里，我们可以很容易分别出OpenTracing和OpenCensus的差别，主要在于OpenCensus把Metrics包括进来了，不仅可以采集traces，还支持采集metrics，还有一点不同OpenCensus并不是单纯的规范制定，他还把包括数据采集的Agent、Collector。

# 附录

[官网](https://opencensus.io/)