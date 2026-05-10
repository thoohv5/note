---
title: sirupsen/logrus
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---

## sirupsen/logrus

[sirupsen/logrus](https://github.com/sirupsen/logrus)

```go

	// 初始化
	logrus.New()
	// 自定义输出字段
	logrus.WithFields(fields Fields)
	// 日志输出路径
	logrus.SetOutput(out io.Writer)
	// 日志输出格式 json,text
	logrus.SetFormatter(formatter Formatter)
	// 显示行号等信息
	logrus.SetReportCaller(include bool)
	// 日志打印级别 panic,fatal,error, warn, info, debug
	logrus.SetLevel(level Level)

  // hook
	type Hook interface {
		Levels() []Level
		Fire(*Entry) error
	}
	logrus.AddHook(hook Hook)
```