# log

### 官方log包

```go
	// 标准输出，格式化fmt
	log.Print()
	// 标准输出，os.exit(1)
	log.Fatal()
	// 标准输出，panic
	log.Panic()

  // 初始化
	log.New(out io.Writer, prefix string, flag int)
  // 日志输出路径 os.Stderr，os.Stdout
	log.SetOutput(w io.Writer)
  // 输出日志的前缀
	log.SetPrefix(prefix string)
	// 定制打印参数
	log.SetFlags(flag int)
	

	// flag 参数
	Ldate         = 1 << iota     // the date in the local time zone: 2009/01/23
	Ltime                         // the time in the local time zone: 01:23:23
	Lmicroseconds                 // microsecond resolution: 01:23:23.123123.  assumes Ltime.
	Llongfile                     // full file name and line number: /a/b/c/d.go:23
	Lshortfile                    // final file name element and line number: d.go:23. overrides Llongfile
	LUTC                          // if Ldate or Ltime is set, use UTC rather than the local time zone
	LstdFlags     = Ldate | Ltime // initial values for the standard logger
```