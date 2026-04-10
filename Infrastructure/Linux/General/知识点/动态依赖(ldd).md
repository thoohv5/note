# 动态依赖(ldd)

## 命令

```
# 可执行文件dependency
ldd /usr/local/sbin/dhcpd

```

### C可执行文件

![](https://raw.githubusercontent.com/thoohv5/ob/main/picturepicture202401261326725.png)

### GO可执行文件

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture202401261337847.png)

## 参数说明

```
Usage: ldd [OPTION]... FILE...
      --help              print this help and exit   （获取指令帮助信息）
      --version           print version information and exit  （打印ldd的版本号）
  -d, --data-relocs       process data relocations   （执行重定位和报告任何丢失的对象）
  -r, --function-relocs   process data and function relocations  （执行数据对象和函数的重定位，并且报告任何丢失的对象和函数）
  -u, --unused            print unused direct dependencies （打印未使用的直接依赖）
  -v, --verbose           print all information   (详细信息模式，打印所有信息，例如包括符号的版本信息)

```

## 工作原理

`ldd` 能够显示可执行模块的`dependency`，其原理是通过设置一系列的环境变量

- LD_TRACE_LOADED_OBJECTS
- LD_WARN
- LD_BIND_NOW
- LD_LIBRARY_VERSION
- LD_VERBOSE
- LD_DEBUG

当LD_TRACE_LOADED_OBJECTS环境变量不为空时，任何可执行程序在运行时，它都会只显示模块的`dependency`，而程序并不真正执行。

ldd默认开启的环境变量是：LD_TRACE_LOADED_OBJECTS=1
其他的变量(和值)分别对应一些选项：

```
-d, --data-relocs -> LD_WARN=yes
-r, --function-relocs ->LD_WARN和LD_BIND_NOW=yes
-u, --unused -> LD_DEBUG="unused"
-v, --verbose -> LD_VERBOSE=yes

```

LD_TRACE_LOADED_OBJECTS为必要环境变量，其他视具体情况。

ldd显示可执行模块的`dependency`的工作原理，其实质是通过`ld-linux.so`(elf动态库的装载器)来实现的。我们知道，`ld-linux.so`模块会先于executable模块程序工作，并获得控制权，因此当上述的那些环境变量被设置时，`ld-linux.so`选择了显示可执行模块的`dependency`。
ldd命令的本质是执行了:`/lib/ld-linux.so.*`

实际上可以直接执`ld-linux.so`模块，如：`/lib/ld-linux.so.2 --list program`(等同`ldd program`)
`ldd`可以获得的共享库文件，其实是通过读取`ldconfig`命令组建起来的文件(`/etc/ld.so.cache`)。

默认的共享库文件搜索/lib优先于/usr/lib，而且也只有这个2个目录。如果想要加入其他路径，则需要通过`ldconfig`命令配置相关文件。

一般`ld-linux.so`会按照以下顺序搜索共享库:

1、DT_RPATH或DT_RUNPATH段
2、环境变量LD_LIBRARY_PATH
3、/etc/ld.so.cache文件中的路径，但如果可执行程序在连接时候添加了-z nodeflib选项，则跳过。
4、默认路径/lib和/usr/lib，但如果添加了-z nodeflib，则跳过。