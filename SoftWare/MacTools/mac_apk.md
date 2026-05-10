---
title: mac apk
date: 2026-04-07
tags: [软件工具, Mac工具]
type: guide
status: complete
---

## mac apk

### 步骤

### 使用apktool工具对APK进行反编译

1. 下载`apktool`
2. 移动`apktool`脚本和`apltool.jar`文件`/usr/local/bin`文件中
3. 授予`apktool`脚本和`apktool.jar`文件可执行的权限 `chmod +x`
4. 检测安装，终端输入`apktool`
5. 复制需要反编译的apk文件到`/usr/local/bin`文件夹中（确保`apktool`、`apktool.jar`以及apk文件都在`/usr/local/bin`目录下
6. 执行反编译命令
    
    ```bash
    apktool d xxx.apk
    or
    apktool d -s xxx.apk
    ```
    
7. 执行完毕后目录下会生成反编译apk的对应文件夹

### 使用dex2jar工具把classes.dex文件转化成.jar文件

1. 下载并解压`dex2jar`
2. 进入`dex2jar`文件夹下
3. 把`classes.dex`文件复制到解压后的`dex2jar`文件夹下
4. 执行如下命令
    
    ```bash
    sh d2j-dex2jar.sh classes.dex
    ```
    
5. 执行完毕后，会在文件夹下生成一个.jar文件，里面就是java源码

### 使用JD-GUI软件查看jar包下的java源码

1. 下载并安装`jd-gui`
2. 打开安装好的JD-GUI，直接把第二步生成的.jar文件拖进去，就能看到反编译出来的java源码了

### 工具

### apktool

[Apktool - How to Install](https://ibotpeaches.github.io/Apktool/install/)

### dex2jax

[dex2jar](https://sourceforge.net/projects/dex2jar/files/)

### JD-GUI

[Java Decompiler](http://java-decompiler.github.io/)