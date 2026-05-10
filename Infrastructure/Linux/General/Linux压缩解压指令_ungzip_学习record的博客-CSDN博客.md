---
title: Linux压缩解压指令_ungzip_学习record的博客-CSDN博客
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: guide
status: complete
---

## Linux压缩解压指令_ungzip_学习record的博客-CSDN博客

[https://blog.csdn.net/qq_60787657/article/details/125828842](https://blog.csdn.net/qq_60787657/article/details/125828842)

为什么要学压缩解压？

我们在向其他人数据传输时没有压缩而是直接传输时，如果传输的文件夹中有多个文件，那么在传输时可能就会有某些文件丢失。而先压缩再传输，不会使文件丢失，保证了传输的可靠性。

基本语法： 压缩：gzip 文件名

解压：ungzip 压缩文件名

举例：

将/home下的date.txt文件压缩---------------------gzip /home/date.txt

```
[root@localhost home]# gzip date.txt[root@localhost home]# lldrwx------.  5 jake animal  107 Jul 16 11:52 jakedrwx------. 15 lyj  lyj    4096 Jul 11 20:43 lyjdrwx------.  3 1001   1001   78 Jul 16 09:30 milandrwxr-xr-x.  2 root root     45 Jul 17 09:28 test
```

将/home下的date.txt.gz 解压-------------ungzip date.txt.gz

```
[root@localhost home]# gunzip date.txt.gz

```

### zip/unzip 指令（压缩时不动原文件）（压缩文件或文件夹）

基本语法：压缩：zip [选项] 文件名.zip 文件或文件夹

解压：unzip [选项] 解压地址 压缩文件名

选项：

- r 递归压缩目录（压缩文件夹时使用）

举例：

将文件夹test 压缩---------------------zip -r test.zip test

```
zip error: Nothing to do! (test.zip)[root@localhost home]# zip -r test.zip test  adding: test/ (stored 0%)  adding: test/test (deflated 2%)  adding: test/test.zip (stored 0%)  adding: test/ooo (stored 0%)[root@localhost home]# lldrwxr-xr-x.  2 root root      6 Jul 17 10:13 newdrwxr-xr-x.  2 root root     45 Jul 17 09:28 test-rw-r--r--.  1 root root    870 Jul 17 10:40 test.zip
```

将压缩文件 test.zip 解压到 /home/csdn 目录下--------------unzip -d /home/csdn /home/test.zip

```
[root@localhost home]# unzip -d /home/csdn/ /home/test.zipArchive:  /home/test.zip   creating: /home/csdn/test/  inflating: /home/csdn/test/test extracting: /home/csdn/test/test.zip extracting: /home/csdn/test/ooo[root@localhost home]# cd csdn/[root@localhost csdn]# lltotal 4-rw-r--r--. 1 root root 29 Jul 15 09:15 date.txtdrwxr-xr-x. 2 root root 45 Jul 17 09:28 test
```

### tar指令（压缩时不动原文件）（压缩文件或文件夹）

基本语法：压缩：tar [选项] 压缩文件名称.tar.gz 原文件名称

解压：tar [选项] 压缩文件名称.tar.gz 解压路径

选项：

| 选项 | 解释 |
| --- | --- |
| -z | 打包，同时压缩 |
| -c | 产生.tar.gz 文件 |
| -v | 显示详细信息 |
| -f | 指定压缩后的文件名 |
| -x | 解压 .tar.gz 文件 |

举例：

压缩文件夹 new -----------------tar -zcvf new1.tar.gz new

```
[root@localhost home]# tar -zcvf new1.tar.gz newnew/[root@localhost home]# lldrwx------.  3 1001   1001   78 Jul 16 09:30 milandrwxr-xr-x.  2 root root      6 Jul 17 10:13 new-rw-r--r--.  1 root root    107 Jul 17 11:04 new1.tar.gzdrwxr-xr-x.  2 root root     45 Jul 17 09:28 test
```

解压文件new1.tar.gz 到/home/csdn--------------------tar -zxvf new.tar.gz -C /home/csdn

```
[root@localhost home]# tar -zxvf new1.tar.gz -C /home/csdn/new/[root@localhost home]# cd csdn/[root@localhost csdn]# lltotal 4-rw-r--r--. 1 root root 29 Jul 15 09:15 date.txtdrwxr-xr-x. 2 root root  6 Jul 17 10:13 newdrwxr-xr-x. 2 root root 45 Jul 17 09:28 test
```

注意：选项中的 vf 的顺序不能改变，改变会报错

## 一、zip/gzip 命令

1️⃣zip命令参数列表：

1. `a`将文件转成 ASCII 模式
2. `F`尝试修复损坏的压缩文件
3. `h`显示帮助界面
4. `m`将文件压缩之后，删除源文件
5. `n`特定字符串 不压缩具有特定字尾字符串的文件
6. `o`将压缩文件内的所有文件的最新变动时间设为压缩时候的时间
7. `q`安静模式，在压缩的时候不显示指令的执行过程
8. `r`将指定的目录下的所有子目录以及文件一起处理
9. `S`包含系统文件和隐含文件(S是大写)
10. `t 日期`把压缩文件的最后修改日期设为指定的日期，日期格式为mmddyyyy

2️⃣举例：

1. 压缩目录： `zip/gzip –q –r script.zip /home/xxp/script`
2. 压缩文件： `zip/gzip -q .cshrc.zip /home/xxp/.cshrc`

3️⃣注意：

1. 如果不指定解压后的文件，会把原文件替换掉
2. zip 必须要用 unzip 命令进行解压，而 gzip 压缩的文件可以用 ungzip 解压，也可以用 gzip 加 -d 选项进行解压。

## 二、unzip/ungzip命令

1️⃣unzip命令参数列表：

1. `a`对文本文件进行必要的字符转换。
2. `b`不要对文本文件进行字符转换。
3. `c`将解压缩的结果显示到屏幕上，并对字符做适当的转换。
4. `p`与`c`参数类似，会将解压缩的结果显示到屏幕上，但不会执行任何的转换。
5. `C`压缩文件中的文件名称区分大小写。
6. `f`更新现有的文件。
7. `u`与`f`参数类似，但是除了更新现有的文件外，也会将压缩文件中的其他文件解压缩到目录中。
8. `j`不处理压缩文件中原有的目录路径。
9. `L`将压缩文件中的全部文件名改为小写。
10. `l`显示压缩文件内所包含的文件。
11. `M`将输出结果送到more程序处理。
12. `n`解压缩时不要覆盖原有的文件。
13. `o`不必先询问用户，unzip执行后覆盖原有文件。
14. `P<密码>`使用zip的密码选项。
15. `q`执行时不显示任何信息。
16. `s`将文件名中的空白字符转换为底线字符。
17. `t`检查压缩文件是否正确。
18. `V`保留VMS的文件版本信息。
19. `v`执行是时显示详细的信息。
20. `X`解压缩时同时回存文件原来的UID/GID。
21. `z`仅显示压缩文件的备注文字。

[.zip文件] 指定.zip压缩文件。

[文件] 指定要处理.zip压缩文件中的哪些文件。

-d<目录> 指定文件解压缩后所要存储的目录。

-x<文件> 指定不要处理.zip压缩文件中的哪些文件。

-Z unzip -Z等于执行zipinfo指令

2️⃣举例：

1. 将/home/wwwroot/xahot.zip解压到当前目录 unzip xahot.zip
2. unzip [-cflptuvz][-agCjLMnoqsVX][-P <密码>][.zip文件][文件][-d <目 录>][-x <文件>] 或 unzip [-Z] 补充说明：unzip为.zip压缩文件的解压缩程序。
3. 如果出现这个提示： -bash: zip: command not found 不能执行ZIP压缩，是因为没有安装ZIP， 运行下这条安装命令即可 yum install zip

## 三、tar命令

1️⃣tar命令参数列表：

tar -cvf /usr/local/auto_bak/test.tar /usr/local/test 仅打包，不压缩

tar -zcvf /usr/local/auto_bak/test.tar.gz /usr/local/test 打包后，以gzip压缩 在参数f后面的压缩文件名是自己取的，习惯上用tar来做，如果加z参数，则以tar.gz 或tgz来代表gzip压缩过的tar file文件

解压操作:

tar -zxvf /usr/local/test.tar.gz

tar 解压缩命令详解

-c: 建立压缩档案

- x：解压

-t：查看内容

-r：向压缩归档文件末尾追加文件

-u：更新原压缩包中的文件

这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

- z：有gzip属性的

-j：有bz2属性的

-Z：有compress属性的

-v：显示所有过程

-O：将文件解开到标准输出

`tar -tzf test.tar.gz`列出“压缩文件test”中的文件列表

2️⃣举例：

下面的参数-f是必须的

- f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。
1. `tar -cf all.tar *.jpg` 这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。
2. `tar -rf all.tar *.gif` 这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。
3. `tar -uf all.tar logo.gif` 这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。
4. `tar -tf all.tar` 这条命令是列出all.tar包中所有文件，-t是列出文件的意思
5. `tar -xf all.tar` 这条命令是解出all.tar包中所有文件，-x是解开的意思

3️⃣压缩：

1. `tar -zcvf test.tar.gz test` 将“文件test”压缩为 testTar.tar.gz 文件，-z 表示用 gzip 压缩
2. `tar –cvf jpg.tar *.jpg` 将目录里所有jpg文件打包成tar.jpg
3. `tar –czf jpg.tar.gz *.jpg` 将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一 个gzip压缩过的包，命名为jpg.tar.gz
4. `tar –cjf jpg.tar.bz2 *.jpg` 将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
5. `tar –cZf jpg.tar.Z *.jpg` 将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
6. `rar a jpg.rar *.jpg` rar格式的压缩，需要先下载rar for linux
7. `zip jpg.zip *.jpg` zip格式的压缩，需要先下载zip for linux

4️⃣解压：

1. `tar -zxvf test.tar.gz` 解压 test.tar.gz 文件
2. `tar –xvf file.tar` 解压 tar包
3. `tar -xzvf file.tar.gz` 解压tar.gz
4. `tar -xjvf file.tar.bz2` 解压 tar.bz2
5. `tar –xZvf file.tar.Z` 解压tar.Z
6. `unrar e file.rar` 解压rar
7. `unzip file.zip` 解压zip

5️⃣总结：

1. `.tar`用 tar –xvf 解压
2. `.gz`用 gzip -d或者gunzip 解压
3. `.tar.gz`和`.tgz`用 tar –xzf 解压
4. `.bz2`用 bzip2 -d或者用bunzip2 解压
5. `.tar.bz2`用tar –xjf 解压
6. `.Z`用 uncompress 解压
7. `.tar.Z`用tar –xZf 解压
8. `.rar`用 unrar e解压
9. `.zip`用 unzip 解压