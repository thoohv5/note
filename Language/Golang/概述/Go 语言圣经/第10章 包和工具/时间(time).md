---
title: 时间(time)
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: complete
---

## 时间(time)

## 获取

### 当前

```go
// 当前时间
fmt.Println(time.Now())            // 2021-01-11 16:11:52.932251 +0800 CST m=+0.000175737
// 当前时间戳（秒）
fmt.Println(time.Now().Unix())     // 1610352712
// 当前时间戳（纳秒）
fmt.Println(time.Now().UnixNano()) // 1610352712932445000
// 当前年月日
fmt.Println(time.Now().Date())     // 2021 January 11
// 当前时分秒
fmt.Println(time.Now().Clock())    // 16 14 19
```

### 格式化时间

### 时间戳转时间字符串

```go
// 格式化 年-月-日 时:分:秒 
fmt.Println(time.Now().Format("2006-01-02 15:03:04")) // 2021-01-11 16:04:16
// 格式化 年-月-日 
fmt.Println(time.Now().Format("2006-01-02")) // 2021-01-11
// 格式化 时:分:秒 
fmt.Println(time.Now().Format("15:03:04")) // 16:04:16
```

### 时间字符串转时间戳

```go
// 格式化 时间戳（当前时区）
fmt.Println(time.ParseInLocation("2006-01-02 15:04:05", "2016-11-02 16:14:25", time.Local)) // 2016-11-02 16:14:25 +0800 CST <nil>
// 格式化 时间戳（零时区）
fmt.Println(time.Parse("2006-01-02 15:04:05", "2016-11-02 16:14:25")) // 2016-11-02 16:14:25 +0000 UTC <nil>
```

## 时间比较

```go
t1, _ := time.ParseDuration("1h1m1s")
now := time.Now()
t1, _ := time.ParseDuration("1h1m1s")
t2, _ := time.ParseDuration("-1h1m1s")
// Add
fmt.Println(now.Add(t1)) // 2021-01-11 17:31:17.591043 +0800 CST m=+3661.000391163
fmt.Println(now.Add(t2)) // 2021-01-11 15:29:15.591054 +0800 CST m=-3660.999598407

// Sub
fmt.Println(now.Sub(now.Add(t1))) // -1h1m1s
// Since
fmt.Println(time.Since(now.Add(t1))) // -1h1m0.999982131s
// Until
fmt.Println(time.Until(now.Add(t1))) // 1h1m0.999979717s
```

## 日期比较

```go
now := time.Now()
t, _ := time.ParseDuration("1h")

// After
fmt.Println(now.Add(t).After(now)) // true
// Before
fmt.Println(now.Add(t).Before(now)) // false
// Equal
fmt.Println(now.Add(t).Equal(now))  // false
```