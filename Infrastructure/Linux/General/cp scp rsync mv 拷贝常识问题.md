---
title: cp scp rsync mv 拷贝常识问题
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: complete
---

## cp scp rsync mv 拷贝常识问题

### cp

```
## new_dir 不存在，命令相当于拷贝出dir的备份（dir => new_dir）; new_dir 存在，命令相当于拷贝出dir的备份到new_dir下
cp -r dir new_dir
## 等同与上面
cp -r dir new_dir/
## 等同于上面
cp -r dir/ new_dir
## 等同于上面
cp -r dir/ new_dir/

```

```
## dir目录下的文件不包含dir目录本身拷贝到new_dir；new_dir 不存在会报错
cp -r dir/* new_dir
## 等同于上面
cp -r dir/* new_dir/

```

### scp

同CP

### rsync

```

## new_dir 不存在时创建文件夹，然后把dir目录下的文件包含dir目录本身拷贝到new_dir
rsync -avcP dir new_dir
## 等同于上面
rsync -avcP dir new_dir/

## new_dir 不存在时创建文件夹，然后把dir目录下的文件不包含dir目录本身拷贝到new_dir
rsync -avcP dir/ new_dir
## 等同与上面
rsync -avcP dir/ new_dir/
## 等同于上面
rsync -avcP dir/* new_dir
## 等同于上面
rsync -avcP dir/* new_dir/

```