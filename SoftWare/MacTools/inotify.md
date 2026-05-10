---
title: inotify
date: 2026-04-07
tags: [软件工具, Mac工具]
type: guide
status: complete
---

## inotify

monitoring filesystem events

inotifywait, fsnotifywait - wait for changes to files using inotify or fanotify

inotifywatch, fsnotifywatch - gather filesystem access statistics using inotify or fanotify

### **是否支持inotify**

```bash
## 使用 uname -r 命令检查Linux内核，如果低于2.6.13，就需要重新编译内核加入inotify的支持。

## 使用ll /proc/sys/fs/inotify命令，是否有以下三条信息输出，如果没有表示不支持

$ ll /proc/sys/fs/inotify
 
total 0
-rw-r--r-- 1 root root 0 Jan 4 15:41 max_queued_events
-rw-r--r-- 1 root root 0 Jan 4 15:41 max_user_instances
-rw-r--r-- 1 root root 0 Jan 4 15:41 max_user_watches
```

### 安装

### CentOS

```bash
yum install inotify-tools
```

### 源码编译

```bash
wget http://github.com/downloads/rvoicilas/inotify-tools/inotify-tools-3.14.tar.gz
tar zxf inotify-tools-3.14.tar.gz
cd inotify-tools-3.14/
./configure && make && make install
```

### 参数

```bash
## 输出帮助信息
-h,–help
## 排除不需要监视的文件，可以是相对路径，也可以是绝对路径。
@
## 从文件读取需要监视的文件或排除的文件，一个文件一行，排除的文件以@开头。
–fromfile
## 接收到一个事件而不退出，无限期地执行。默认的行为是接收到一个事情后立即退出。
-m, –monitor
## 跟–monitor一样，除了是在后台运行，需要指定–outfile把事情输出到一个文件。也意味着使用了–syslog。
-d, –daemon
## 输出事件到一个文件而不是标准输出。
-o, –outfile
## 输出错误信息到系统日志
-s, –syslog
## 监视一个目录下的所有子目录。
-r, –recursive
## 指定一次，不会输出详细信息，指定二次，除了致命错误，不会输出任何信息。
-q, –quiet
## 正则匹配需要排除的文件，大小写敏感。
–exclude <pattern>
## 正则匹配需要排除的文件，忽略大小写。
–excludei <pattern>
## 设置超时时间，如果为0，则无限期地执行下去。
-t , –timeout
## 指定监视的事件。
-e , –event
## 输出csv格式。
-c, –csv
## 指定时间格式，用于–format选项中的%T格式。（%y年 %m月 %d日 %H小时 %M分钟）
–timefmt
## 指定输出格式。（%T时间 %w路径 %f文件名 %e状态）
	%w 表示发生事件的目录
	%f 表示发生事件的文件
	%e 表示发生的事件
	%Xe 事件以“X”分隔
	%T 使用由–timefmt定义的时间格式
–format

```

### 事件

```bash
access	访问，读取文件。
 
modify	修改，文件内容被修改。
 
attrib	属性，文件元数据被修改。
 
move	移动，对文件进行移动操作。
 
create	创建，生成新文件
 
open	打开，对文件进行打开操作。
 
close	关闭，对文件进行关闭操作。
 
delete	删除，文件被删除。
```

### 示例

```bash
#!/bin/bash
 
projectDir=/production/sites/vue-program;
 
while inotifywait $projectDir/src -r --timefmt '%d/%m/%y %H:%M' --format "%T %f" -e MODIFY --exclude '^.*.swp$'
do
    cd $projectDir && git pull && npm run build
done
```

## 附录

[Home · inotify-tools/inotify-tools Wiki](https://github.com/inotify-tools/inotify-tools/wiki)