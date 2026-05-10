---
title: 文件操作（File Op）
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## 文件操作（File Op）

os包

```go
func Mkdir(name string, perm FileMode) error 
func Chdir(dir string) error
func TempDir() string
func Rename(oldpath, newpath string) error
func Chmod(name string, mode FileMode) error
func Open(name string) (*File, error) {
	return OpenFile(name, O_RDONLY, 0)
}
func Create(name string) (*File, error) {
	return OpenFile(name, O_RDWR|O_CREATE|O_TRUNC, 0666)
}
func OpenFile(name string, flag int, perm FileMode) (*File, error) {
	testlog.Open(name)
	return openFileNolog(name, flag, perm)
}
```

预定义的Flag值

```
O_RDONLY int = syscall.O_RDONLY // 只读打开文件和os.Open()同义
O_WRONLY int = syscall.O_WRONLY // 只写打开文件	
O_RDWR   int = syscall.O_RDWR   // 读写方式打开文件	
O_APPEND int = syscall.O_APPEND // 当写的时候使用追加模式到文件末尾	
O_CREATE int = syscall.O_CREAT  // 如果文件不存在，此案创建	
O_EXCL   int = syscall.O_EXCL   // 和O_CREATE一起使用，只有当文件不存在时才创建
O_SYNC   int = syscall.O_SYNC   // 以同步I/O方式打开文件，直接写入硬盘
O_TRUNC  int = syscall.O_TRUNC  // 如果可以的话，当打开文件时先清空文件
```

ioutil包

```go
func ReadFile(filename string) ([]byte, error) // f, err := os.Open(filename)
func WriteFile(filename string, data []byte, perm os.FileMode) error  //os.OpenFile
func ReadDir(dirname string) ([]os.FileInfo, error) //	f, err := os.Open(dirname)
```