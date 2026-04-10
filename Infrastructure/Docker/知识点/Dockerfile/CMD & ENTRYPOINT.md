# CMD & ENTRYPOINT

当 CLI 命令`docker run`中带有参数时，

1. 守护进程将忽略 Dockerfile 中定义的 CMD 指令。
2. ENTRYPOINT 不会被忽略，命令行上的参数被附加到 ENTRYPOINT 指定的命令的参数列表中。

# CMD

**为容器提供默认的执行命令。**

一个 Dockerfile 可以有一个或多个 CMD 指令，在有多个CMD 指令的情况下，除最后一个外，其余的都会被忽略。

最好的使用 CMD 指令的时机是：

**当用户在命令行上不输入参数时，为容器提供默认的执行命令。**

该指令通过在容器镜像运行后立即启动应用程序来确保容器处于运行状态。之所以这样，CMD 指令会在容器启动时立即加载基础镜像。

# **ENTRYPOINT**

在 Dockerfile 中，ENTRYPOINT 指令用于设置在启动容器时始终运行的可执行文件。与 CMD 指令不同，ENTRYPOINT 指令不能被忽略或重写（准确的说`docker run`后面跟的命令行参数，不会覆盖ENTRYPOINT指令；`docker run`的`--entrypoint`可以覆盖 Dockerfile 中 ENTRYPOINT 设置的命令），即使容器运行时声明了命令行参数。

Docker ENTRYPOINT 指令支持 shell 和 exec 两种模式的写法:

- Exec 模式: `ENTRYPOINT ["executable", "parameter1", "parameter2"]`
    - Exec 特殊模式：`ENTRYPOINT ["sh", "-c", "executable parameter1 parameter2"]`
- Shell 模式: `ENTRYPOINT command parameter1 parameter2`

**ENTRYPOINT 指令为 [exec 模式](https://zhida.zhihu.com/search?content_id=171732125&content_type=Article&match_order=1&q=exec+模)8F&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDQwMTA4NjUsInEiOiJleGVjIOaooeW8jyIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjE3MTczMjEyNSwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.Ui53B3yvyD1mr8qASOHh1-WZb5VR7PKuusz-YJqMhF4&zhida_source=entity时，命令行上指定的参数（或者CMD参数）会作为参数添加到 ENTRYPOINT 指定命令的参数列表中。**

**ENTRYPOINT 指令为 shell 模式时，会忽略命令行和 CMD 参数。（因为没有想到好的办法获取动态参数）**

**`shell`**命令格式和**`exec`**命令格式的区别：

对于 shell 形式的 ENTRYPOINT 或者 CMD，docker daemon 会创建一个新的 /bin/bash shell 进程，并在这个新进程中运行你的命令。这意味着你的命令会在一个新的 shell 环境中运行，这个环境有它自己的环境变量，且和启动容器的环境是隔离的。

这与 exec 形式不同，exec 形式直接在 Docker 容器的主进程中运行命令，没有额外创建 shell 进程。这就是为什么 exec 形式可以接收 docker run 附加的参数。

## **ENTRYPOINT 使用变量**

在涉及到使用变量的时候只有shell的方式才可以，exec函数的形式不行

```bash
# 使用如下这种形式设置变量就不行
ENTRYPOINT ["java","${PARAMS}","${JAVA_OPTS}","-Djava.security.egd=file:/dev/./urandom","-jar","demo-0.0.1-SNAPSHOT.jar"]

# 可以使用这种形式
ENTRYPOINT ["sh", "-c", "java ${PARAMS} ${JAVA_OPTS} -Djava.security.egd=file:/dev/./urandom -jar demo-0.0.1-SNAPSHOT.jar"]
```

# 总结

| 名称 | 命令 | docker run command | 动态参数 |
| --- | --- | --- | --- |
| CMD-exec | CMD [“command”, ”param1”, ”param2”] | 覆盖 | 不解析 |
| CMD-exec-shell | CMD [”sh”, “-c”, “command param1 param2”] | 覆盖 | 解析 |
| CMD-shell | CMD command param1 param2 | 覆盖 | 解析 |
| ENTRYPOINT-exec | ENTRYPOINT [“command”, ”param1”, ”param2”] | 追加，默认为CMD参数 | 不解析 |
| ENTRYPOINT-exec-shell | ENTRYPOINT [”sh”, “-c”, “command param1 param2”] | 追加，默认为CMD参数 | 解析 |
| ENTRYPOINT-shell | ENTRYPOINT command param1 param2 | 不追加 | 解析 |

# 实操

**同时使用 CMD 和 ENTRYPOINT**

CMD 和 ENTRYPOINT 是两个功能类似的指令，有些情况可能需要在 Dockerfile 中使用它们的组合指令。可以使用 ENTRYPOINT 指令定义命令，同时使用 CMD 定义参数。

如果 `ENTRYPOINT` 中想使用变量时，建议直接加载`shell`脚本, `CMD` 注入默认参数。

```bash
# ================= DockerFile ===========================

ENTRYPOINT ["sh", "/usr/bin/docker-entrypoint.sh"]

CMD ["server", "--conf", "/data/conf"]

# ================= docker-entrypoint.sh ===========================

#!/bin/sh
set -e

sh /usr/bin/install.sh

exec /app/demo-interface "$@"
```

# 附录

[CMD 命令和 ENTRYPOINT 命令的区别_cmd entrypoint-CSDN博客](https://blog.csdn.net/qq_54015483/article/details/132823037)