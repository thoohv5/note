# Annotation (注解)

# 概述

Annotation与Label类似，也使用key/value键值对的形式进行定义。不同的是Label具有严格的命名规则，它定义的是Kubernetes对象的元数据(Metadata)，并且用于LabelSelector。而Annotation则是用户任意定义的“附加”信息，以便于外部工具进行查找，很多时候,Kubernetes的模块自身会通过Annotation的方式标记资源对象的一些特殊信息。

通常来说，用Annotation来记录的信息如下：

- obuild信息、release信息、Docker镜像信息等，例如时间戳、releaseid号、PR号、镜像hash值、dockerregistry地址等。
- 日志库、监控库、分析库等资源库的地址信息。
- 程序调试工具信息，例如工具名称、版本号等。
- 团队的联系信息，例如电话号码、负责人名称、网址等。