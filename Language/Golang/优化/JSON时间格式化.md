---
title: go 中，json 转换时间（time.Time）的格式（默认格式为 RFC3339）_golang rfc3339_Grassto的博客-CSDN博客
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: guide
status: complete
---

## go 中，json 转换时间（time.Time）的格式（默认格式为 RFC3339）_golang rfc3339_Grassto的博客-CSDN博客

标签: Golang
URL: https://blog.csdn.net/DisMisPres/article/details/119178013?spm=1001.2101.3001.6650.6&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-6-119178013-blog-119931046.pc_relevant_3mothn_strategy_and_data_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-6-119178013-blog-119931046.pc_relevant_3mothn_strategy_and_data_recovery&utm_relevant_index=10
状态: 待完成
类型: 文字

## 简介

go 中使用 `json.Unmarshal` 转换结构体时，若结构体中有**时间类型**作为解析字段时，使用的是国际标准 `RFC3339` （**2006-01-02T15:04:05Z07:00**） 格式来作为默认格式进行解析的。

现[前后端交互](https://so.csdn.net/so/search?q=前后端交)92&spm=1001.2101.3001.7020时间，有三种方式

1. 前端自行做 `RFC3339` 的时间格式转换
2. 后端自定义 `MarshalJSON` 和 `UnmarshalJSON` 方法
3. 前后端统一用时间戳进行交互（**推荐这种方式**）

## 1. 前端的时间转换 `RFC3339`

### 标准格式日期转为 `RFC3339`

```
var toRFC3339 = function(date){
    let y = date.getFullYear()
    let m = date.getMonth()+1<10?'0'+(date.getMonth()+1):(date.getMonth()+1)
    let d = date.getDate()<10?'0'+date.getDate():date.getDate()
    let hh = date.getHours()<10?'0'+date.getHours():date.getHours();
    let mm = date.getMinutes()<10?'0'+date.getMinutes():date.getMinutes()
    let ss = date.getSeconds()<10?'0'+date.getSeconds():date.getSeconds()
    var endDate = y +'-' + m + '-' + d + ' ' + hh + ':' + mm + ':' + ss
    endDate = endDate.replace(/\s+/g, 'T')+'+08:00'
    return endDate
}
1234567891011
```

### RFC3339转为标准格式日期

```
var toTime = function(dateStr) {
    var date = new Date(dateStr).toJSON();
    return newDate=new Date(+new Date(date)+8*3600*1000).toISOString().replace(/T/g,' ').replace(/\.[\d]{3}Z/,'');
}
1234
```

## 2. 后端做转换

自定义 `time` 类型，然后自己实现 `json` 的 `MarshalJSON` 和 `UnmarshalJSON` 方法。以下例子作参考：

```
package jsontest

import (
    "encoding/json"
    "testing"
    "time"
)

type Time time.Time

const (
    timeFormart = "2006-01-02 15:04:05"
)

func (t *Time) UnmarshalJSON(data []byte) (err error) {
    now, err := time.ParseInLocation(`"`+timeFormart+`"`, string(data), time.Local)
    *t = Time(now)
    return
}

func (t Time) MarshalJSON() ([]byte, error) {
    b := make([]byte, 0, len(timeFormart)+2)
    b = append(b, '"')
    b = time.Time(t).AppendFormat(b, timeFormart)
    b = append(b, '"')
    return b, nil
}

func (t Time) String() string {
    return time.Time(t).Format(timeFormart)
}

type Person struct {
    Id       int64  `json:"id"`
    Name     string `json:"name"`
    Birthday Time   `json:"birthday"`
}

func TestTimeJson(t *testing.T) {
    now := Time(time.Now())
    t.Log(now)
    src := `{"id":5,"name":"xiaoming","birthday":"2016-06-30 16:09:51"}`
    p := new(Person)
    err := json.Unmarshal([]byte(src), p)
    if err != nil {
        t.Fatal(err)
    }
    t.Log(p)
    t.Log(time.Time(p.Birthday))
    js, _ := json.Marshal(p)
    t.Log(string(js))
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152
```

## 3. 统一使用[时间戳](https://so.csdn.net/so/search?q=时间)B3&spm=1001.2101.3001.7020

### `go` 获取时间戳（秒）

```
time.Now().Unix()
1
```

### `js` 获取时间戳（毫秒）

```
var d = new Date()
d.getTime()  // 1627457656981
12
```

## `go time` 包源码查看

源代码在 `src/time/time.go` 文件中

### `UnmarshalJSON` 方法

```
func (t *Time) UnmarshalJSON(data []byte) error {
	// Ignore null, like in the main JSON package.
	if string(data) == "null" {
		return nil
	}
	// Fractional seconds are handled implicitly by Parse.
	var err error
	*t, err = Parse(`"`+RFC3339+`"`, string(data))
	return err
}
12345678910
```

### `time` 包支持的其他日期格式

```
const (
	ANSIC       = "Mon Jan _2 15:04:05 2006"
	UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
	RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
	RFC822      = "02 Jan 06 15:04 MST"
	RFC822Z     = "02 Jan 06 15:04 -0700" // RFC822 with numeric zone
	RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
	RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
	RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // RFC1123 with numeric zone
	RFC3339     = "2006-01-02T15:04:05Z07:00"
	RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
	Kitchen     = "3:04PM"
	// Handy time stamps.
	Stamp      = "Jan _2 15:04:05"
	StampMilli = "Jan _2 15:04:05.000"
	StampMicro = "Jan _2 15:04:05.000000"
	StampNano  = "Jan _2 15:04:05.000000000"
)
123456789101112131415161718
```

## 小结

本篇的问题，主要是在前后端做交互时，接口调用报错，后发现 `json` 解析时间错误，前后端的时间格式不一致。

涉及时间的交互，推荐使用**时间戳**来进行交互，这样会避免很多问题。

最后还要提一下，`js` 的 `date.getTime()` 获取到的默认时间戳是**毫秒**，而 `go` 的 `time.Unix()` 获取的默认为**秒**，这个在做前后端交互的时候一定要当心。

## 参考