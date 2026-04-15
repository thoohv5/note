---
title: "每日一Go-84、从 0 到 1：用 Go 打造千万级 Excel 高性能批量处理系统（实战）"
source: "https://mp.weixin.qq.com/s/hQuTF4zosJ44Vt045xwfAA"
author:
  - "[[Coding君]]"
published:
created: 2026-04-15
description: "高并发 + 流式处理 + 内存优化 + 工程化架构，一篇讲透 Go 处理千万级 Excel 的正确姿势。"
tags:
  - "clippings"
---
原创 Coding君 *2026年2月23日 15:31*

![图片](https://mmbiz.qpic.cn/mmbiz_png/icZynHSib9ic6dg6rTIiadO4BUWlEeOSTWNozxKlXicUbV30E82NQibeNMqPeqGNvac4JoRVFP9WeKM469MTUbuvxPH7jIFcG61Dzp9TcCbbokibBQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

---

高并发 + 流式处理 + 内存优化 + 工程化架构，一篇讲透 Go 处理千万级 Excel 的正确姿势。

---

## 一、背景：后端为什么一定会踩 Excel 这个坑？

如果你做过后端系统，大概率遇到过这些场景：

- 财务要导出 **百万 / 千万级报表**
- 运营每天跑 **上百个 Excel 批量清洗**
- BI 系统定时生成 **超大 Excel 文件**
- 客户要求 **历史数据一次性导出**

一开始你可能会写出这样的代码：

```css
rows, _ := f.GetRows("Sheet1")
```

然后：

- 本地测试没问题
- 测试环境开始变慢
- 线上直接 **OOM / 超时 / 卡死**

**当 Excel 行数从 10 万 → 100 万 → 1000 万，处理方式必须彻底改变。**

本文将从 **0 到 1** ，完整带你构建一个 **千万级 Excel 高性能批量处理系统** ，覆盖：

- 架构设计
- 核心实现
- 并发模型
- 内存优化
- 工程化落地方案

---

## 二、核心难点分析

Excel 处理的本质问题，其实只有三个：

### 1️⃣ 内存爆炸（OOM）

传统 Excel 处理库，大多是 **一次性加载整个文件到内存** ：

> 文件 300MB → 内存占用 2GB+

### 2️⃣ 处理速度慢

- 串行解析
- 单线程写入
- 大量字符串序列化

导致：

> 百万级 5 分钟，千万级直接卡死

### 3️⃣ 工程不可扩展

- 业务逻辑耦合在一起
- 代码难维护
- 无法横向扩展

---

## 三、整体架构设计

### 核心设计原则

**一句话总结：**

> 绝不全量加载，一切流式处理；  
> 读、算、写解耦，流水线并发。

---

### 架构总览

```nginx
Excel → 流式读取 → Worker Pool → 业务处理 → 流式写入 → 新 Excel
```

拆解为四个核心模块：

| 模块 | 职责 |
| --- | --- |
| Reader | 流式读取 Excel |
| Dispatcher | 任务分发 + 背压 |
| Worker Pool | 并发业务处理 |
| Writer | 流式写入 Excel |

---

## 四、核心库选型

Go 操作 Excel 主流库对比：

| 库 | 是否支持流式 | 适合规模 |
| --- | --- | --- |
| excelize | ✅ | 百万 ~ 千万 |
| tealeg/xlsx | ❌ | 10万以内 |
| unioffice | ⚠️ | 百万级 |

结论：

> **千万级 Excel 处理，excelize 是最好的选择。**

安装：

```swift
go get github.com/xuri/excelize/v2
```

---

## 五、流式读取：解决 OOM 的关键

### ❌ 错误示范（90% 的人这样写）

```css
rows, _ := f.GetRows("Sheet1")
```

本质问题：

> 一次性加载整个 Sheet → 内存直接爆炸

---

### ✅ 正确方式：流式读取

```css
rows, err := f.Rows("Sheet1")if err != nil {    panic(err)}defer rows.Close()for rows.Next() {    row, _ := rows.Columns()    process(row)}
```

优势：

- 逐行读取
- 常量级内存占用
- 可处理千万级数据

---

## 六、并发处理模型设计（核心）

### 并发架构模型

```nginx
Reader → channel → Worker Pool → channel → Writer
```

核心目标：

> CPU 跑满 + 内存稳定 + 不丢数据

---

### Worker Pool 实现

```go
workerNum := runtime.NumCPU() * 2jobs := make(chan []string, 1000)for i := 0; i < workerNum; i++ {    go func() {        for row := range jobs {            handle(row)        }    }()}
```

推荐配置：

- CPU 密集： `CPU 核数`
- IO + 计算混合： `CPU * 2`

---

### 背压控制（非常重要）

如果 Reader 速度 > Writer 速度：

> channel 会无限堆积 → OOM

解决方案：

- **使用有缓冲 channel**
- 通过阻塞自然形成 **背压机制**

```go
jobs := make(chan []string, 1000)
```

当 channel 满时，Reader 自动阻塞。

---

## 七、流式写入：性能提升 5~10 倍

### ❌ 传统写法

```javascript
f.SetCellValue("Sheet1", "A1", value)f.SaveAs("out.xlsx")
```

问题：

- 单线程写入
- 内存缓存所有单元格
- 数据量一大直接炸

---

### ✅ 正确写法：StreamWriter

```css
sw, err := f.NewStreamWriter("Sheet1")if err != nil {    panic(err)}for i, row := range rows {    axis, _ := excelize.CoordinatesToCellName(1, i+1)    sw.SetRow(axis, row)}sw.Flush()
```

**实测：性能提升 5~10 倍，内存占用稳定。**

---

## 八、完整流水线实现示例

```go
func (e *ExcelPipeline) Run(in, out string) error {    r, err := excelize.OpenFile(in)    if err != nil {        return err    }    w := excelize.NewFile()    rows, err := r.Rows("Sheet1")    if err != nil {        return err    }    defer rows.Close()    jobs := make(chan []string, 1000)    results := make(chan []any, 1000)    workerNum := runtime.NumCPU() * 2    var wg sync.WaitGroup    // 启动 worker 池    for i := 0; i < workerNum; i++ {        wg.Add(1)        go func() {            defer wg.Done()            e.Worker(jobs, results)        }()    }    // 读取 Excel → jobs    go func() {        for rows.Next() {            row, _ := rows.Columns()            jobs <- row        }        close(jobs)    }()    // 等所有 worker 结束 → 关闭 results    go func() {        wg.Wait()        close(results)    }()    sw, err := w.NewStreamWriter("Sheet1")    if err != nil {        return err    }    idx := 1    for res := range results {        axis, _ := excelize.CoordinatesToCellName(1, idx)        sw.SetRow(axis, res)        idx++    }    sw.Flush()    return w.SaveAs(out)}
```

```go
func (e *ExcelPipeline) Worker(jobs <-chan []string, results chan<- []any) {    for job := range jobs {        row := make([]any, 0, len(job))        for _, cell := range job {            row = append(row, cell)        }        results <- row    }}
```

---

## 九、性能实测对比

测试环境：

- CPU：10 核
- 内存：32GB
- 数据规模： **1000 万行 × 10 列**

| 方案 | 耗时 | 峰值内存 |
| --- | --- | --- |
| 普通读写 | OOM | 爆 |
| 流式单线程 | 9m12s | 300MB |
| 流式 + 并发 | **2m30s** | 350MB |

👉 **性能提升约 4倍，内存稳定。**

---

## 十、工程化目录结构推荐

```cs
excel-batch/ ├── cmd/ ├── internal/ │    ├── reader/ │    ├── dispatcher/ │    ├── worker/ │    ├── writer/ │    └── pipeline/ ├── pkg/ └── main.go
```

设计原则：

- 模块解耦
- 可扩展
- 可测试
- 易维护

---

## 十一、生产级优化方案

### 1️⃣ 文件分片

千万级 → 拆分多个 Sheet / 文件

### 2️⃣ 分块流水线

> 每 10 万行一个 chunk，降低峰值内存

### 3️⃣ GC 优化

```javascript
export GOGC=50
```

### 4️⃣ goroutine 数量控制

防止 10w goroutine 把机器打死

---

## 十二、真实业务场景

- 财务月报
- 订单流水导出
- BI 报表系统
- 日志分析平台
- 大数据清洗

这些系统中， **Excel 处理往往是性能瓶颈的第一大户。**

---

## 总结

如果你需要在生产环境处理 **百万 / 千万级 Excel** ，请牢记这三点：

> **流式读取 + 并发处理 + 流式写入**

它们决定了：

- 系统是否 OOM
- 接口是否超时
- 服务是否稳定

---

【7天训练营系列：】（可随时退费）

[Docker7天冲刺训练营](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzYzMjExMTk0Mw==&action=getalbum&album_id=4325948521355886611#wechat_redirect) （小白友好）

---

源码地址\*

1、公众号“Codee君”回复“源码”获取源码

2、https://pan.baidu.com/s/1B6pgLWfSgMngVeFfSTcPdg?pwd=jc1s

---

如果您喜欢这篇文章，请您（点赞、分享、亮爱心），万分感谢！

作者提示: 个人观点，仅供参考

继续滑动看下一个

Codee君

向上滑动看下一个