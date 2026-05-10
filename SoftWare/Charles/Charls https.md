---
title: Charls https
date: 2026-04-07
tags: [软件工具, 抓包工具]
type: note
status: complete
---

## Charls https

## **背景**

使用抓包软件（以 Charles 为例）抓取APP的 https 请求时，Android和Charles都正确安装了证书却出现抓包失败，报错：

Client SSL handshake failed: An unknown issue occurred processing the certificate (certificate_unknown)

[](https://img-blog.csdn.net/20180330132929243?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoYWRvd3lTcGlyaXRz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## **原因**

Android7.0 之后默认不信任用户添加到系统的CA证书：

> To provide a more consistent and more secure experience across the Android ecosystem, beginning with Android Nougat, compatible devices trust only the standardized system CAs maintained in AOSP.（文档链接）
> 

也就是说对基于 SDK24 及以上的APP来说，即使你在手机上安装了抓包工具的证书也无法抓取 https 请求

## **解决方案**

### **一、官方解决方案（需修改代码）**

- 官方文档：[https://developer.android.google.cn/training/articles/security-config.html](https://developer.android.google.cn/training/articles/security-config.html)
- 详细演示：[https://blog.csdn.net/mrxiagc/article/details/75329629](https://blog.csdn.net/mrxiagc/article/details/75329629)

### **二、将抓包软件的证书安装成系统证书（需ROOT）**

系统证书目录：`/system/etc/security/cacerts/`

其中的每个证书的命名规则如下：`<Certificate_Hash>.<Number>`文件名是一个Hash值，而后缀是一个数字。

文件名可以用下面的命令计算出来：

`openssl x509 -subject_hash_old -in <Certificate_File>`

后缀名的数字是为了防止文件名冲突的，比如如果两个证书算出的Hash值是一样的话，那么一个证书的后缀名数字可以设置成0，而另一个证书的后缀名数字可以设置成1

### **操作步骤：**

将抓包软件的证书用上述命令计算出 Hash 值，将其改名并复制到系统证书目录

[](https://img-blog.csdn.net/20180330140909423?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoYWRvd3lTcGlyaXRz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

此时你应该可以在 设置->安全->加密与凭据->信任的凭据 的系统标签页看到你新加入的证书，将其启用即可顺利抓包

[](https://img-blog.csdn.net/20180330140221230?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoYWRvd3lTcGlyaXRz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)