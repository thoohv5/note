---
title: CPUlimit
date: 2026-04-07
  - 软件工具
type: note
status: complete
---

## CPUlimit

## 安装

```bash
wget -O cpulimit.zip <https://github.com/opsengine/cpulimit/archive/master.zip>
unzip cpulimit.zip
cd cpulimit-master
make
sudo cp src/cpulimit /usr/bin

```

### 高级用法

```bash

## 后台运行
cpulimit -e command --limit 50 --background

## 终止 CPU 用量过高的进程(默认：限制进程的 CPU 用量上限值，如果进程超过这个上限值，预设会调节 CPU 用量)
cpulimit -e command --limit 50 --kill

## 自动离开
## 在默认的状况下，cpulimit 在执行时若没有发现指定的进程（或是指定的进程已经中止了），它还是会持续等待并监控系统的进程，只要有发现符合条件的进程，就会继续进行 CPU 用量的控制。
cpulimit -e command --limit 50 --lazy

```

### 脚本示例

```bash
command &

cpulimit --pid $! --limit 50

```

### 持续监听

```bash
#!/bin/bash
## auth:kaliarch
## func:sys info check
## version:v1.0
## sys:centos6.x/7.x

set -e
[ $(id -u) -gt 0 ] && exit 1

## cpu使用超过百分之多少进行限制
PEC_CPU=20

## 限制进程使用百分之多少,如果程序为多线程，单个cpu限制为85，如果为多核心，就需要按照比例写，例如cpu为2c，像限制多线程占比80%，就写170
LIMIT_CPU=30
## 日志
LOG_DIR=/data/cpulimit/log/

## 超过阀值进程pid
PIDARG=$(ps -aux |awk -v CPU=${PEC_CPU} '{if($3 > CPU) print $2}')
CPULIMITCMD=$(which cpulimit)

install_cpulimit() {
    [ ! -d /tmp ] && mkdir /tmp || cd /tmp
    wget -c <https://github.com/opsengine/cpulimit/archive/v0.2.tar.gz>
    tar -zxf v0.2.tar.gz
    cd cpulimit-0.2 && make
    [ $? -eq 0 ] && cp src/cpulimit /usr/bin/
}

do_cpulimit() {
[ ! -d ${LOG_DIR} ] && mkdir -p ${LOG_DIR}
for i in ${PIDARG};
do
        MSG=$(ps -aux |awk -v pid=$i '{if($2 == pid) print $0}')
        echo ${MSG}
              [ ! -d /tmp ] && mkdir /tmp || cd /tmp
              nohup ${CPULIMITCMD} -p $i -l ${LIMIT_CPU} &
        echo "$(date) -- ${MSG}" >> ${LOG_DIR}$(date +%F).log
done
}

main() {

    hash cpulimit
    if [ $? -eq 0 ];then
        do_cpulimit
    else
        install_cpulimit && do_cpulimit
    fi
}

main

```