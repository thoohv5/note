---
title: 当通过ldflags选项传递给go命令时，w标志是什么意思？(What does the w flag mean when passed in via the ldflags option to the go command?)_电脑培训
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## 当通过ldflags选项传递给go命令时，w标志是什么意思？(What does the w flag mean when passed in via the ldflags option to the go command?)_电脑培训

标签: Golang
URL: https://www.656463.com/wenda/dtgldflagsxxcdjgomlswbzssmys_373
状态: 待完成
类型: 文字

```
 语境： 去1.2，Ubuntu 12.10 目标： 减少编译的二进制文件的大小 目前在我的构建过程中，我运行“go install”来生成二进制文件。 我从某个地方读到，如果我通过-w它会缩小二进制。 我通过将它传递到-ldflags选项来尝试它，我的二进制文件丢失了1MB大小。 这是-w标志记录在任何地方？ 它实际上做了什么？ 然后我发现strip -s <binary>命令并在-w上运行它，并且又得到了750KB的减重！ 生成的二进制运行良好。 剥离在任何情况下都会造成问题吗？
Context:go 1.2, ubuntu 12.10Goal:Reduce size of compiled binariesCurrently in my build process, I run "go install" to generate the binary. The I read from somewhere that if I pass in-w it will shrink the binary. I tried it by passing it into the-ldflags option & my binary lost 1MB in size.Is this-w flag documented anywhere? What does it actually do?I then discovered thestrip -s <binary> command and ran that on top of-w and got another weight loss of 750KB ! The resulting binary runs fine. Does stripping cause problems in any situations ?
```

### 最满意答案

```
 如果使用-ldflags'-w -s'进行编译，您将获得最小的二进制文件。 -w关闭DWARF调试信息：由于所有元数据gdb所需的内容都不会包含在内，所以您将无法在二进制文件上使用gdb查看特定函数或设置断点或获取堆栈跟踪。 您也将无法使用其他依赖于信息的工具，如pprof分析。 -s关闭Go符号表的生成：您将无法使用'go tool nm'列出二进制文件中的符号。 strip -s就像传递给-ldflags一样，但是它不会去掉很多东西。 '去工具纳米'可能仍然工作'strip -s'后。 我不完全确定。 这些 - 不是-ldflags -w，不是-ldflags -s，而不是strip -s - 都不会影响实际程序的执行。 它们只影响您是否可以使用其他工具调试或分析程序。
You will get the smallest binaries if you compile with -ldflags '-w -s'. The -w turns off DWARF debugging information: you will not be able to use gdb on the binary to look at specific functions or set breakpoints or get stack traces, because all the metadata gdb needs will not be included. You will also not be able to use other tools that depend on the information, like pprof profiling. The -s turns off generation of the Go symbol table: you will not be able to use 'go tool nm' to list the symbols in the binary. Strip -s is like passing -s to -ldflags but it doesn't strip quite as much. 'Go tool nm' might still work after 'strip -s'. I am not completely sure.None of these - not -ldflags -w, not -ldflags -s, not strip -s - should affect the execution of the actual program. They only affect whether you can debug or analyze the program with other tools.
```

### 相关问答

[更多](https://www.656463.com/wenda)

- 从命令链接引用： -X importpath.name=value Set the value of the string variable in importpath named name to value. Note that before Go 1.5 this option took two separate arguments. Now it takes one argument split on the first = sign. 因此它可以用于任何包，而不仅仅用于ma ...
    
    ## [如何在Golang构建中使用-ldflags -X来设置包变量(How to set package variable using -ldflags -X in Golang build)](https://www.656463.com/wenda/rhzGolanggjzsyldflagsXlszbbl_94)*[2022-05-31]*
    
- 问题是，当一个进程使用一个可执行文件的PE头中 的“subsystem”变量设置为“Windows”时创建时，该进程有三个标准句柄关闭， 并且不与任何控制台关联 - 无论如何 ，从控制台运行它或不。 （事实上，如果您运行的子系统不是从控制台设置为“console”的可执行文件，则为该进程强制创建了一个控制台，并且该进程已连接到该进程 - 通常将其视为一个控制台窗口，弹出所有突然间。） 因此，要从Windows上的GUI进程向控制台打印任何内容，必须将该进程显式连接到连接到其父进程（如果有）的控制台，如此处所 ...
    
    ## [使用-ldflags -H = windowsgui编译golang应用程序时，将输出打印到命令窗口(Printing output to a command window when golang application is compiled with -ldflags -H=windowsgui)](https://www.656463.com/wenda/syldflagsHwindowsguibygolangyycx_117)*[2022-07-31]*
    
- 免责声明：我不是编译工具链和可执行文件格式的专家。 我会尽量不说愚蠢的事情，但如果你看到任何错误，请纠正我！ 我在带有x86_64架构的ArchLinux笔记本电脑上运行这些测试。 Go版本是1.8.1。 首先，我们需要知道实际使用这些标志的位置： $ go help build ... -ldflags 'flag list' arguments to pass on each go tool link invocation ... 显然，标志只是传递给go tool link调用。 看看帮助， ...
    
    ## [如何检查golang二进制文件是否用--ldflags =“ - s -w”编译(how to check whether golang binary is compiled with --ldflags=“-s -w”)](https://www.656463.com/wenda/rhjcgolangejzwjsfyldflagsswby_155)*[2022-11-29]*
    
- 你实际上可能有两个问题。 我建议你试试： 将-I../../usr/local/sys/usr/include到CFLAGS以使其找到标题。 将LDFLAGS更改为-L../../usr/local/sys/usr/lib ，添加LIBS=-lxml2并将链接器调用更改为$(LD) $(LDFLAGS) -o $(OUTDIR)/$(NAME) $(OBJ) $(LIBS) （即在末尾添加-l ，在链接器命令行的开头添加-L ）。 You actually may have two problems. I ...
    
    ## [使用clang / LLVM通过LDFLAGS链接.dylib(linking .dylib via LDFLAGS using clang/LLVM)](https://www.656463.com/wenda/syclangLLVMtgLDFLAGSljdylib_208)*[2022-12-11]*
    
- 使用我的配置（QtCreator + Visual C ++编译器），makefile中定义的LFLAGS将提供给链接器，与GNU make文档中所述相反： LDFLAGS：当编译器应该调用链接器时给予编译器的额外标志[...] LFLAGS：给Lex的额外标志。 我认为您应该尝试将QMAKE_LFLAGS += "-z noexecstack -z relro -z now"到您的.pro文件中，清空您的构建文件夹，重新运行qmake，并查看该选项是否已提供给链接器。 With my configurat ...
    
    ## [Qt pro文件中的LDFLAGS(LDFLAGS in Qt pro file)](https://www.656463.com/wenda/QtprowenjianzhongdeLDFLAGS_217)*[2023-03-14]*
    
- 如果使用-ldflags'-w -s'进行编译，您将获得最小的二进制文件。 -w关闭DWARF调试信息：由于所有元数据gdb所需的内容都不会包含在内，所以您将无法在二进制文件上使用gdb查看特定函数或设置断点或获取堆栈跟踪。 您也将无法使用其他依赖于信息的工具，如pprof分析。 -s关闭Go符号表的生成：您将无法使用'go tool nm'列出二进制文件中的符号。 strip -s就像传递给-ldflags一样，但是它不会去掉很多东西。 '去工具纳米'可能仍然工作'strip -s'后。 我不完全确定。 ...
    
    ## [当通过ldflags选项传递给go命令时，w标志是什么意思？(What does the w flag mean when passed in via the ldflags option to the go command?)](https://www.656463.com/wenda/dtgldflagsxxcdjgomlswbzssmys_373)*[2022-09-18]*
    
- 如果这个错误抱怨它所说的是抱怨的（并且我完全不确定它为什么/应该如此）那么我认为解决方案就像添加一样简单 AM_LDFLAGS = 作为使用+=的foreach循环上方的行。 从第8.7 Variables used when building a program节开始8.7 Variables used when building a program GNU Automake手册8.7 Variables used when building a program ： AM_LDFLAGS 这是Makef ...
    
    ## [AM_LDFLAGS和LDFLAGS之间有什么区别？(what is the difference between AM_LDFLAGS and LDFLAGS)](https://www.656463.com/wenda/AM_LDFLAGShLDFLAGSzjysmqb_401)*[2022-10-31]*
    
- 我认为ifeq块不应该进入make目标，而应该进入makefile的前面（在所有:)之前。 就是这样： OS:=$(shell uname) DST=hello SRC=$(wildcard *.cpp) OBJ=$(SRC:.cpp=.o) CFLAGS= ifeq ($(OS),Darwin) $(info OS is $(OS)) CC=g++ LDFLAGS=-lm -framework OpenCL CFLAGS+=-O3 endif ifeq ($(OS),L ...
    
    ## [根据当前操作系统更新makefile中的CFLAGS或LDFLAGS(Update CFLAGS or LDFLAGS in makefile depending on current OS)](https://www.656463.com/wenda/gjdqczxtgxmakefilezdCFLAGShLDFLA_474)*[2022-09-11]*
    
- 在curl_exec($ch)你可以使用curl_getinfo($ch, CURLINFO_*); 返回CURLINFO_*常量的值。 http://php.net/manual/en/function.curl-getinfo.php This may be one of the following constants: CURLINFO_EFFECTIVE_URL - Last effective URL CURLINFO_HTTP_CODE - Last received HTTP code CU ...
    
    ## [PHP curl中的-w选项？(-w option in PHP curl?)](https://www.656463.com/wenda/PHPcurlzhongdewxuanxiang_582)*[2022-06-22]*