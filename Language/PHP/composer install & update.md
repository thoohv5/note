---
title: composer install & update
date: 2026-04-07
tags: [编程语言, PHP]
type: guide
status: complete
---

# composer install & update

当你执行`composer update`的时候，composer会去读取`composer.json`中指定的依赖，去分析他们，并且去拉取符合条件最新版本的依赖。然后他会把所拉取到的依赖放入vendor目录下，并且把所有拉取的依赖的精确版本号写入`composer.lock`文件中

`composer install`所执行的事情非常类似，只在第一步的时候有差别。当你本地如果已经存在一份`composer.lock`时，它将会去读取你的`composer.lock`而非`composer.json`，并且以此为标准去下载依赖。当你本地没有`composer.lock`的时候，它所做的事情和`composer update`其实并没有区别。