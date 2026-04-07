# 重启策略(RestartPolicy)

# 概述

`RestartPolicy` 是 Docker 容器的一种配置选项，用于定义在容器退出后如何重新启动该容器。它决定了容器在停止后是否会自动重新启动，以及在什么条件下重新启动。

# 选项

| **旗帜** | **描述** |
| --- | --- |
| `no` | 不要自动重启容器。 （**默认**） |
| `on-failure[:max-retries]` | **当容器非0退出码**时（即容器崩溃时）重启。 如果是手动停止的，只有在 Docker daemon 重启或者容器本身手动重启时才会重启。（可选）限制 Docker 守护进程尝试使用 `:max-retries`选项。 |
| `always` | **无论何时容器退出**（包括正常退出和崩溃退出）都会重启，**除非容器是被手动停止的**。如果是手动停止的，只有在 Docker daemon 重启或者容器本身手动重启时才会重启。 |
| `unless-stopped` | 如同 `always`，除了当容器停止（手动或其他方式）时，即使在 Docker 守护程序重新启动后也不会重新启动。 |

# **查看容器的 RestartPolicy 配置**

你可以使用 `docker inspect` 命令查看容器的重启策略：

```bash
docker inspect <container_name_or_id> --format '{{.HostConfig.RestartPolicy.Name}}
```

该命令将返回容器的重启策略，比如 `no`、`always`、`unless-stopped` 或 `on-failure`。

### 查看容器重启次数

```bash
docker inspect -f "{{ .RestartCount }}" nginx-server
```

### 查看容器最后一次的启动时间

```bash
docker inspect -f "{{ .State.StartedAt }}" nginx-server
```

# **更改容器的 RestartPolicy**

如果容器已经创建并运行，你可以通过 `docker update` 命令更改容器的重启策略：

```bash
docker update --restart=always <container_name_or_id>
```

## Docker容器的退出状态码

docker run的退出状态码如下：

- 0 -表示正常退出
- 非0 -表示异常退出（退出状态码采用chroot标准）
- 125 -Docker守护进程本身的错误
- 126 -容器启动后，要执行的默认命令无法调用
- 127 -容器启动后，要执行的默认命令不存在
- **137-容器被强制终止**
- 其他命令状态码，容器启动后正常执行命令，退出命令时该命令的返回状态码作为容器的退出状态码

# 实验

```bash

#################always######################

# 正常退出：【重启】
docker run --name test-container --restart=always alpine /bin/sh -c "sleep 5 && echo 'done'"

# 非正常退出：【重启】
docker run --name test-container --restart=always alpine /bin/sh -c "sleep 5 && exit 1"

# 手动退出：【不重启】/ Docker daemon 重启：【重启】
docker stop test-container

#################unless-stopped######################

# 正常退出：【重启】
docker run --name test-container --restart=unless-stopped alpine /bin/sh -c "sleep 5 && echo 'done'"

# 非正常退出：【重启】
docker run --name test-container --restart=unless-stopped alpine /bin/sh -c "sleep 5 && exit 1"

# 手动退出：【不重启】/ Docker daemon 重启：【不重启】
docker stop test-container

#################on-failure######################

# 正常退出：【不重启】
docker run --name test-container --restart=on-failure alpine /bin/sh -c "sleep 5 && echo 'done'"

# 非正常退出：【重启】
docker run --name test-container --restart=on-failure alpine /bin/sh -c "sleep 5 && exit 1"

# 手动退出：【不重启】/ Docker daemon 重启：【重启】
docker stop test-container

#################no######################

# 正常退出：【不重启】
docker run --name test-container --restart=no alpine /bin/sh -c "sleep 5 && echo 'done'"

# 非正常退出：【不重启】
docker run --name test-container --restart=no alpine /bin/sh -c "sleep 5 && exit 1"

# 手动退出：【不重启】
docker stop test-container

```