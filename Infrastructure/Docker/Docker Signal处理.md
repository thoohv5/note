---
title: Docker Signal处理
date: 2026-04-07
tags:
  - 基础设施
  - Docker
type: note
status: complete
---

## Docker Signal处理

Docker对Linux Signal也做了很多的支持。

## **`docker stop`命令信号支持**

当我们用docker stop命令来停掉容器的时候，**docker默认会允许容器中的应用程序有10秒的时间用以终止运行**。我们可以通过在执行docker stop命令时手动指定--time/-t参数来自定义一个stop时间长度。

```bash
> docker stop --help
Usage:  docker stop [OPTIONS] CONTAINER [CONTAINER…]
Stop one or more running containers
Options:
      --help      Print usage
  -t, --time int  Seconds to wait for stop before killing it (default 10)
```

**在docker stop命令执行的时候，会先向容器中PID为1的进程(main process)发送系统信号SIGTERM，然后等待容器中的应用程序终止执行，如果等待时间达到设定的超时时间，如默认的10秒，会继续发送SIGKILL的系统信号强行kill掉进程。在容器中的应用程序，可以选择忽略和不处理SIGTERM信号，不过一旦达到超时时间，程序就会被系统强行kill掉。**

## **`docker kill`命令信号支持**

默认情况下，docker kill命令不会给容器中的应用程序有任何gracefully shutdown的机会，它会直接发出SIGKILL的系统信号以强行终止容器中程序的运行。

查看docker kill命令的帮助我们看到，除了默认发送SIGKILL信号外，还允许我们发送一些自定义的系统信号：

```bash
> docker kill --help
Usage:  docker kill [OPTIONS] CONTAINER [CONTAINER…]
Kill one or more running containers
Options:
      --help            Print usage
  -s, --signal string  Signal to send to the container (default "KILL")
```

比如，如果我们想向docker中的程序发送SIGINT信号，我们可以这样来实现：

```bash
docker kill --signal=SIGINT container_name
```

与docker stop命令不一样的地方在于，docker kill没有任何的超时时间设置，它会直接发送SIGKILL信号，或者用户指定的其他信号。

## **`docker rm`命令信号支持**

docker rm命令用于删除已经停止运行的容器，我们可以添加--force或-f参数强行删除正在运行的容器。使用这个参数后，docker会先给运行中的容器发送SIGKILL信号，强制停掉容器，然后再做删除。

例如，强制删除正在运行的名称为web容器。

```bash
docker rm -fv web
```

## **`docker daemon`进程对信号支持**

docker daemon进程会接收SIGHUP信号，接收后会重新reload daemon.json配置文件。

我们为dockerd进程发送一个SIGHUP信号：

```bash
kill -SIGHUP $(pidof dockerd)

或者

systemctl reload docker
```

查看docker daemon的日志可以看到，docker daemon接收这个信号并重新reload daemon.json配置文件

```bash
> journalctl -u docker.service -f
-- Logs begin at Sun 2018-01-07 09:17:01 CST. --
Jan 18 16:20:11 vm10-1-1-28.ksc.com dockerd[26668]: time="2018-01-18T16:20:11.262904839+08:00" level=info msg="Got signal to reload configuration, reloading from: /etc/docker/daemon.json"
Jan 18 16:21:41 vm10-1-1-28.ksc.com systemd[1]: Reloading Docker Application Container Engine.
```

所以在你修改完/etc/docker/daemon.json文件后，可以直接给Docker发送一个SIGHUP信号实现配置文件的reload，而不需要重启docker daemon。

> 注意：`systemctl reload docker` 命令通常不会导致机器上的容器重启。这个命令的作用是让 Docker 守护进程重新加载其配置文件，而不会中断正在运行的容器。它和 `systemctl restart docker` 是不同的，后者会停止并重新启动 Docker 服务，从而导致所有容器重启。
> 

## 附录

[Docker容器优雅退出 - 人艰不拆_zmc - 博客园](https://www.cnblogs.com/zhangmingcheng/p/18252004)