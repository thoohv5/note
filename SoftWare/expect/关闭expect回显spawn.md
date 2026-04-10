---
title: 关闭expect执行spawn时回显spawn - 道霖 - 博客园
date: 2026-04-07
tags: [软件工具]
type: note
status: complete
---

# 关闭expect执行spawn时回显spawn - 道霖 - 博客园

[https://www.cnblogs.com/t-road/p/16336072.html](https://www.cnblogs.com/t-road/p/16336072.html)

当我们执行expect时，比如：

expect <<EOF

set timeout -1

spawn ssh 192.168.0.108 "ls /home"

expect {

"password" { send "echoyang\r" }

"yes/no" { send "yes\r";exp_continue }

eof {

exit 1

}

}

wait

EOF

在结果中，它首先回显我们执行的命令，然后再显示结果：

[root@localhost ~]# sh test.sh

spawn ssh 192.168.0.108 ls /home

apps

如果不想回显spawn，则添加参数：

expect <<EOF

set timeout -1

spawn **-noecho** ssh 192.168.0.108 "ls /home"

expect {

"password" { send "echoyang\r" }

"yes/no" { send "yes\r";exp_continue }

eof {

exit 1

}

}

wait

EOF

此时再执行脚本，结果为：

[root@localhost ~]# sh test.sh

apps