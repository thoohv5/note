# Go for range

# Go 1.22 之前

for,range 的k,v变量在整个遍历过程中共用，不能直接进行引用传递，即地址传递，但循环内定义的变量只能为当前循环所使用。

# Go 1.22 及其 之后

已修复

# 附录

[golang for,range的坑](https://www.jianshu.com/p/7349b443eed4)

[Go 1.22 is released! - The Go Programming Language](https://go.dev/blog/go1.22)

[Fixing For Loops in Go 1.22 - The Go Programming Language](https://go.dev/blog/loopvar-preview)