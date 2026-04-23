---
title: Composer
date: 2026-04-07
  - 软件工具
type: note
status: complete
---

## Composer

## 四种模式

composer 自动载入 autoload 的使用详解

### psr-0 标准 autoload_namespaces

懒加载，将目标目录作为基目录再进行命名空间和路径的映射后继续向后加载

```go
// php 的 psr-0 规范的自动载入，是将目标目录作为命名空间的基目录再进行路径映射载入类文件
"psr-0": {
    "Psr0\\Lib\\": "psr0/lib/src/"
}
```

<aside>
💡 这里还有一点是需要注意的，下划线 _ 对 psr-0 是有特殊意义的。psr-0 的加载器会将类名中的 _ 解析成目录分隔符。

</aside>

### psr-4 标准 autoload_psr4

懒加载，将目标目录直接映射为命名空间对应的目录继续向后加载

```go
// php 的 psr-4 规范的自动载入，是将目标目录直接影射为命名空间的
"psr-4": {
    "Psr4\\Lib\\": "psr4/lib/src/",
    "App\\Controllers\\": "app/controllers/",
    "App\\Models\\": "app/models/"
}
```

### classmap 模式 autoload_classmap

懒加载，扫描目录下的所有类文件，支持递归扫描， 生成对应的类名=>路径的映射，当载入需要的类时直接取出路径，速度最快

```go
// classmap 扫描目录下的所有类文件 生成对应的类名=>路径的映射
"classmap": [
    "classmap/lib/src/"
]
```

### files 模式

自动载入的文件，主要用来载入一些没办法懒加载的公共函数

```go
// 扫描目录下的所有文件生成 hash => 路径的映射  运行时实时加载
// 主要用来载入工具函数
"files": [
    "ext/common/functions.php",
    "ext/system/functions.php"
]
```

## getLoader

![](Composer/Untitled.png)

### 线上问题

由于项目设计没有使用composer.lock、composer私有库以及构建环境的复杂性问题导致，composer 无法构建，采用手动上传Vendor的方式替代，上传的Vendor存在问题导致`Class xxx does not exist`

[composer 自动载入 autoload 的使用详解 psr0/psr4/classmap/files](https://my.oschina.net/sallency/blog/893518)