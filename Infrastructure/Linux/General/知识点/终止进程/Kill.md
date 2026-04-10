# Kill

kill - terminate a process

## 用途

kill 命令很容易让人产生误解，以为它仅仅就是用来杀死进程的。我们来看一下 man page 对它的解释：kill - send a signal to a process.从官方的解释不难看出，**kill 是向进程发送信号的命令**。当然我们可以向进程发送一个终止运行的信号，此时的 kill 命令才是名至实归。事实上如果我们不给 kill 命令传递信号参数，它**默认传递终止进程运行的信号给进程**！这是 kill 命令最主要的用法，也是本文要介绍的内容。

一般情况下，终止一个前台进程使用 Ctrl + C 就可以了。对于一个后台进程就得用 kill 命令来终止。我们会先使用 ps、top 等命令获得进程的 PID，然后使用 kill 命令来杀掉该进程。

```go
kill 命令格式
kill -l

1(HUP) 重新加载进程
9(KILL) 杀死进程
15(TREM) 完美地停止以一个进程，默认

```

## 可用的进程信号

```
[root@localhost ~]# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX

```

### 常用信号

| 信号代号 | 信号名 | 说明 |
| --- | --- | --- |
| 1 | SIGHUP | 进程立即关闭，重新读取配置文件之后重启 |
| 2 | SIGINT | 程序终止信号，用于终止前台进程。相当于`ctrl+c` |
| 8 | SIGFPE | 发生致命的算数运算错误时触发 |
| 9 | SIGKILL | 程序强制终止进程，不能被阻塞、处理和忽略 |
| 14 | SIGALRM | 时钟定时信号，计算的是实际时间或时钟时间 |
| 15 | SIGTERM | 程序正常结束信号，kill命令的默认信号 |
| 18 | SIGCONT | 让暂停的进程恢复执行，不能被阻断 |
| 19 | SIGSTOP | 暂停前台进程，相当于`ctrl+z` |