---
title: 通用网关接口（CGI Common Gateway Interface）
date: 2026-04-07
tags: [编程语言, PHP]
type: reference
status: complete
---

# 通用网关接口（CGI Common Gateway Interface）

## CGI

Web Server 与 Web Application 之间数据交换的一种协议

### 工作原理

web服务器收到用户请求，就会把请求提交给cgi程序（如php-cgi），cgi程序根据请求提交的参数作应处理（解析php），然后输出标准的html语句，返回给web服服务器，WEB服务器再返回给客户端

**fork-and-execute模式：**每一次web请求都会有启动和退出过程

## FastCGI

常驻(long-live)型的CGI

### 工作原理

FastCGI接口方式采用C/S结构，可以将HTTP服务器和脚本解析服务器分开，同时在脚本解析服务器上启动一个或者多个脚本解析守护进程。当HTTP服务器每次遇到动态程序时，可以将其直接交付给FastCGI进程来执行，然后将得到的结果返回给浏览器。这种方式可以让HTTP服务器专一地处理静态请求，或者将动态脚本服务器的结果返回给客户端，提高了整个应用系统的性能。

### PHP-CGI

PHP实现的自带的FastCGI管理器

### PHP-FPM，PHP FastCGI Process Manager

PHP FastCGI 进程管理器，用于管理PHP 进程池的软件，用于接受web服务器的请求。

PHP-FPM提供了更好的PHP进程管理方式，可以有效控制内存和进程、可以平滑重载PHP配置。

## ISAPI

## WSGI