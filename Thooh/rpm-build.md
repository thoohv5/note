---
title: rpm-build
date: 2023-12-12
categories: Linux
description: RPM 包管理器 （RPM） 是一个运行在 Red Hat Enterprise Linux、CentOS 和 Fedora 上的包管理系统。
share: "true"
---

# 概述

RPM 包管理器 （RPM） 是一个运行在 Red Hat Enterprise Linux、CentOS 和 Fedora 上的包管理系统。RPM 使您能够更轻松地分发、管理和更新为红帽企业 Linux、CentOS 和 Fedora 创建的软件。许多软件供应商通过传统的存档文件（如压缩包）分发他们的软件。

# 依赖

```
# centos7
yum install -y gcc rpm-build rpm-devel rpmlint make python bash coreutils diffutils patch rpmdevtools

# Centos8
dnf install -y gcc rpm-build rpm-devel rpmlint make python bash coreutils diffutils patch rpmdevtools

```

## 工作目录

RPM制作的工作目录，用于源代码解压，编译，安装以及打包的场所

```
# 生成工作目录
rpmdev-setuptree
```

| 目录    | 宏          | 作用                                                         |
| ------- | ----------- | ------------------------------------------------------------ |
| BUILD   | `%_buildder`  | 用户存放解压后的源码，并源码的编译在该目录完成，会产生临时目录%buildroot |
| RPMS    | `%_rpmdir`    | 存放生成的二进制rpm包                                        |
| SOURCES | `%_sourcedir` | 存放源代码压缩包和补丁                                       |
| SRPMS   | `%_srcrpmdir` | 存放生成的源码rpm包                                          |

### .spec 文件

#### 文件生成

```
rpmdev-newspec xxx
```

#### 文件配置说明
| 项目                                                                | 说明                                                                                   |     |
| ------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | --- |
| Name                                                                | 软件名，应与SPEC文件名匹配                                                             |     |
| Version                                                             | 软件上游版本号                                                                         |     |
| Release                                                             | 发布编号，默认：1%{?dist}                                                              |     |
| Summary                                                             | 简要说明                                                                               |     |
| License                                                             | 软件包的协议版本，GPLv3+                                                               |     |
| URL                                                                 | 项目信息的网站                                                                         |     |
| Source0                                                             | 压缩包资源链接，如果需要，可以添加更多SourceX指令，每次地递增数据                      |     |
| Patch0                                                              | 补丁包资源链接，如果需要，可以添加更多 PatchX 指令，每次递增数量                       |     |
| BuildArch                                                           | 支持体系架构，如果完全使用解释性编程语言编写，请将其设置为noarch                       |     |
| ExcludeArch                                                         | 排除体系架构                                                                           |     |
| BuildRequires                                                       | %build阶段所需的各种依赖，逗号隔开                                                     |     |
| Requires                                                            | %install阶段所需的各种依赖                                                             |     |
| %description                                                        | 完成描述，可跨越多行                                                                   |     |
| %prep<br /><br />%setup -q                                          | 将SOURCE目录下的源码解压至BUILD目录下，为%build阶段做好准备，可执行shell脚本           |     |
| %build                                                              | 编译BUILD目录下的源码，生成二进制文件，为之后的%install阶段做好准备                    |     |
| %configure<br /><br />make `%{?_smp_mflags}`                        |                                                                                        |     |
| %install<br /><br />rm -rf $RPM_BUILD_ROOT<br /><br />%make_install | 读取BUILD目录下需要安装的文件，将其预安装至BUILDROOT目录下，可加入脚本文件或者配置文件 |     |
| %check                                                              | 测试软件命令                                                                           |     |
| %files                                                              | 需要被打包的文件和目录                                                                 |     |
| %doc                                                                |                                                                                        |     |
| %changelog                                                          |                                                                                        |     |
|                                                                     |                                                                                        |     |

## 命令

```
rpmbuild option xxx.spec

# option
-bp #只执行spec的%pre段(解开源码包并打补丁,即只做准备)
-bc #执行spec的%pre和%build 段(准备并编译)
-bi #执行spec中%pre,%build与%install(准备,编译并安装)
-bl #检查spec中的%file段(查看文件是否齐全)
-ba #建立源码与二进制包(常用):即编译后做成*.rpm和*.src.rpm
-bb #只建立二进制包(常用):即编译后做成*.rpm
-bs #只建立源码包:即只做成*.src.rpm
```


## 常用宏路径

```
%{_sysconfdir}       /etc
%{_prefix}           /usr
%{_exec_prefix}      %{_prefix}
%{_bindir}           %{_exec_prefix}/bin
%{_libdir}           %{_exec_prefix}/%{_lib}
%{_libexecdir}       %{_exec_prefix}/libexec
%{_sbindir}          %{_exec_prefix}/sbin
%{_sharedstatedir}   /var/lib
%{_datarootdir}      %{_prefix}/share
%{_datadir}          %{_datarootdir}
%{_includedir}       %{_prefix}/include
%{_infodir}          /usr/share/info
%{_mandir}           /usr/share/man
%{_localstatedir}    /var
%{_initddir}         %{_sysconfdir}/rc.d/init.d
%{_var}              /var
%{_tmppath}          %{_var}/tmp
%{_usr}              /usr
%{_usrsrc}           %{_usr}/src
%{_lib}              lib (lib64 on 64bit multilib systems)
%{_docdir}           %{_datadir}/doc
%{buildroot}         %{_buildrootdir}/%{name}-%{version}-%{release}.%{_arch}
$RPM_BUILD_ROOT      %{buildroot}
```


# 附录

https://rpm-packaging-guide.github.io/
