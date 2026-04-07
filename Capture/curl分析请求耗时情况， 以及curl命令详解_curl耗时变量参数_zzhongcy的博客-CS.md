# curl分析请求耗时情况， 以及curl命令详解_curl耗时变量参数_zzhongcy的博客-CSDN博客

标签: Design
URL: https://blog.csdn.net/zzhongcy/article/details/23372535
状态: 待完成
类型: 文字

最近项目要用[curl](https://so.csdn.net/so/search?q=curl&spm=1001.2101.3001.7020)进行http测试，干脆就把crul的命令看了看：

`curl` 命令提供了 `-w` 参数，这个参数在 manpage 是这样解释的：

```
 -w, --write-out <format>              Make curl display information on stdout after a completed transfer. The format is a string that may contain plain text mixed with any number of variables. The  format              can  be  specified  as  a literal "string", or you can have curl read the format from a file with "@filename" and to tell curl to read the format from stdin you write              "@-".              The variables present in the output format will be substituted by the value or text that curl thinks fit, as described below. All variables are specified  as  %{vari‐              able_name} and to output a normal % you just write them as %%. You can output a newline by using \n, a carriage return with \r and a tab space with \t.
```

它能够按照指定的格式打印某些信息，里面可以使用某些特定的变量，而且**支持 `\n`、`\t`和 `\r` 转义字符**。提供的变量很多，比如 `status_code`、`local_port`、`size_download` 等等，这篇文章我们只关注和请求时间有关的变量（以 `time_` 开头的变量）。

先往文本文件 `curl-format.txt` 写入下面的内容：

```
➜  ~ cat curl-format.txt    time_namelookup:  %{time_namelookup}\n       time_connect:  %{time_connect}\n    time_appconnect:  %{time_appconnect}\n      time_redirect:  %{time_redirect}\n   time_pretransfer:  %{time_pretransfer}\n time_starttransfer:  %{time_starttransfer}\n                    ----------\n         time_total:  %{time_total}\n
```

那么这些变量都是什么意思呢？我解释一下：

- `time_namelookup`：DNS 域名解析的时候，就是把 `https://zhihu.com` 转换成 ip 地址的过程
- `time_connect`：TCP 连接建立的时间，就是三次握手的时间
- `time_appconnect`：SSL/SSH 等上层协议建立连接的时间，比如 connect/handshake 的时间
- `time_redirect`：从开始到最后一个请求事务的时间
- `time_pretransfer`：从请求开始到响应开始传输的时间
- `time_starttransfer`：从请求开始到第一个字节将要传输的时间
- **`time_total`：这次请求花费的全部时间**

我们先看看一个简单的请求，没有重定向，也没有 SSL 协议的时间：

```
➜  ~ curl -o /dev/null -w "@curl-format.txt" -s -L "http://cizixs.com"    time_namelookup:  0.012       time_connect:  0.227    time_appconnect:  0.000      time_redirect:  0.000   time_pretransfer:  0.227 time_starttransfer:  0.443                    ----------         time_total:  0.867
```

可以看到这次请求各个步骤的时间都打印出来了，每个数字的单位都是秒（seconds），这样可以分析哪一步比较耗时，方便定位问题。这个命令各个参数的意义：

- `w`：从文件中读取要打印信息的格式
- `o /dev/null`：把响应的内容丢弃，因为我们这里并不关心它，只关心请求的耗时情况
- `s`：不要打印进度条

从这个输出，我们可以算出各个步骤的时间：

- DNS 查询：12ms
- **TCP 连接时间：pretransfter(227) - namelookup(12) = 215ms**
- **服务器处理时间：starttransfter(443) - pretransfer(227) = 216ms**
- **内容传输时间：total(867) - starttransfer(443) = 424ms**

来个比较复杂的，访问某度首页，带有中间有重定向和 SSL 协议：

```
➜  ~ curl -w "@curl-format.txt" -o /dev/null -s -L "https://baidu.com"    time_namelookup:  0.012       time_connect:  0.018    time_appconnect:  0.328      time_redirect:  0.356   time_pretransfer:  0.018 time_starttransfer:  0.027                    ----------         time_total:  0.384
```

可以看到 `time_appconnect` 和 `time_redirect` 都不是 0 了，其中 SSL 协议处理时间为 `328-18=310ms`。而且 `pretransfer` 和 `starttransfer` 的时间都缩短了，这是重定向之后请求的时间。

# 2、全部命令参数

Usage: curl [options...] <url>

Options: (H) means HTTP/HTTPS only, (F) means FTP only

- -anyauth Pick "any" authentication method (H)

-a, --append 上传文件时，附加到目标文件 (F/SFTP)

--basic 使用HTTP基本验证 (H)

--cacert FILE CA证书 (SSL)

--capath DIR CA证书目录 (SSL)

-E, --cert CERT[:PASSWD] 客户端证书文件和密码 (SSL)

--cert-type TYPE 证书文件类型 (DER/PEM/ENG) (SSL)

--ciphers LIST SSL ciphers to use (SSL)

--compressed 要求返回是压缩的形势 (using deflate or gzip)

-K, --config FILE Specify which config file to read

--connect-timeout SECONDS 设置最大请求时间

-C, --continue-at OFFSET 断点续转OFFSET

**-b, --cookie STRING/FILE String or file to read cookies from (H)**

-c, --cookie-jar FILE 操作结束后把cookie写入到这个文件中 (H)

--create-dirs 建立本地目录的目录层次结构

--crlf 上传是把LF转变成CRLF

--crlfile FILE Get a CRL list in PEM format from the given file

**-d, --data DATA HTTP POST方式传送数据DATA (H)**

--data-ascii DATA HTTP POST ASCII data (H)

--data-binary DATA HTTP POST binary data (H)

--data-urlencode DATA HTTP POST data url encoded (H)

--delegation STRING GSS-API delegation permission

--digest 使用HTTP数字身份验证 (H)

--disable-eprt 禁止使用EPRT或LPRT (F)

--disable-epsv 禁止使用EPSV (F)

--dns-servers DNS server addrs to use: 1.1.1.1;2.2.2.2

--dns-interface Interface to use for DNS requests

--dns-ipv4-addr IPv4 address to use for DNS requests, dot notation

--dns-ipv6-addr IPv6 address to use for DNS requests, dot notation

-D, --dump-header FILE 把header信息写入到该文件中

--egd-file FILE 为随机数据(SSL)设置EGD socket路径 (SSL)

--engine ENGINE Crypto engine (SSL). "--engine list" for list

-f, --fail 连接失败时不显示http错误 (H)

**-F, --form CONTENT 模拟http表单提交数据 (H)**

--form-string STRING 模拟http表单提交数据 (H)

--ftp-account DATA Account data string (F)

--ftp-alternative-to-user COMMAND String to replace "USER [name]" (F)

--ftp-create-dirs 如果远程目录不存在，创建远程目录 (F)

--ftp-method [MULTICWD/NOCWD/SINGLECWD] 控制CWD的使用 (F)

--ftp-pasv 使用 PASV/EPSV 代替PORT (F)

-P, --ftp-port ADR Use PORT with given address instead of PASV (F)

--ftp-skip-pasv-ip 使用PASV的时候,忽略该IP地址 (F)

--ftp-pret Send PRET before PASV (for drftpd) (F)

--ftp-ssl-ccc Send CCC after authenticating (F)

--ftp-ssl-ccc-mode ACTIVE/PASSIVE Set CCC mode (F)

--ftp-ssl-control Require SSL/TLS for ftp login, clear for transfer (F)

**-G, --get 以get的方式来发送数据 (H)**

-g, --globoff 禁用网址序列和范围使用{}和[]

**-H, --header LINE 自定义头信息传递给服务器 (H)**

-I, --head Show document info only

-h, --help This help text

--hostpubmd5 MD5 Hex encoded MD5 string of the host public key. (SSH)

-0, --http1.0 Use HTTP 1.0 (H)

--http1.1 Use HTTP 1.1 (H)

--http2 Use HTTP 2 (H)

--ignore-content-length Ignore the HTTP Content-Length header

-i, --include Include protocol headers in the output (H/F)

-k, --insecure Allow connections to SSL sites without certs (H)

--interface INTERFACE Specify network interface/address to use

-4, --ipv4 Resolve name to IPv4 address

-6, --ipv6 Resolve name to IPv6 address

-j, --junk-session-cookies Ignore session cookies read from file (H)

--keepalive-time SECONDS Interval between keepalive probes

--key KEY Private key file name (SSL/SSH)

--key-type TYPE Private key file type (DER/PEM/ENG) (SSL)

--krb LEVEL Enable Kerberos with specified security level (F)

--libcurl FILE Dump libcurl equivalent code of this command line

--limit-rate RATE Limit transfer speed to this rate

-l, --list-only List only mode (F/POP3)

--local-port RANGE Force use of these local port numbers

**-L, --location Follow redirects (H)**

--location-trusted like --location and send auth to other hosts (H)

-M, --manual Display the full manual

--mail-from FROM Mail from this address (SMTP)

--mail-rcpt TO Mail to this/these addresses (SMTP)

--mail-auth AUTH Originator address of the original email (SMTP)

--max-filesize BYTES Maximum file size to download (H/F)

--max-redirs NUM Maximum number of redirects allowed (H)

-m, --max-time SECONDS Maximum time allowed for the transfer

--metalink Process given URLs as metalink XML file

--negotiate Use HTTP Negotiate Authentication (H)

-n, --netrc Must read .netrc for user name and password

--netrc-optional Use either .netrc or URL; overrides -n

--netrc-file FILE Set up the netrc filename to use

-: --next Allows the following URL to use a separate set of options

--no-alpn Disable the ALPN TLS extension (H)

-N, --no-buffer Disable buffering of the output stream

--no-keepalive Disable keepalive use on the connection

--no-npn Disable the NPN TLS extension (H)

--no-sessionid Disable SSL session-ID reusing (SSL)

--noproxy List of hosts which do not use proxy

--ntlm Use HTTP NTLM authentication (H)

--oauth2-bearer TOKEN OAuth 2 Bearer Token (IMAP, POP3, SMTP)

-o, --output FILE Write output to <file> instead of stdout

--pass PASS Pass phrase for the private key (SSL/SSH)

--post301 Do not switch to GET after following a 301 redirect (H)

--post302 Do not switch to GET after following a 302 redirect (H)

--post303 Do not switch to GET after following a 303 redirect (H)

-#, --progress-bar Display transfer progress as a progress bar

--proto PROTOCOLS Enable/disable specified protocols

--proto-redir PROTOCOLS Enable/disable specified protocols on redirect

-x, --proxy [PROTOCOL://]HOST[:PORT] Use proxy on given port

--proxy-anyauth Pick "any" proxy authentication method (H)

--proxy-basic Use Basic authentication on the proxy (H)

--proxy-digest Use Digest authentication on the proxy (H)

--proxy-negotiate Use Negotiate authentication on the proxy (H)

--proxy-ntlm Use NTLM authentication on the proxy (H)

-U, --proxy-user USER[:PASSWORD] Proxy user and password

--proxy1.0 HOST[:PORT] Use HTTP/1.0 proxy on given port

-p, --proxytunnel Operate through a HTTP proxy tunnel (using CONNECT)

--pubkey KEY Public key file name (SSH)

-Q, --quote CMD Send command(s) to server before transfer (F/SFTP)

--random-file FILE File for reading random data from (SSL)

-r, --range RANGE Retrieve only the bytes within a range

--raw Do HTTP "raw", without any transfer decoding (H)

-e, --referer Referer URL (H)

-J, --remote-header-name Use the header-provided filename (H)

-O, --remote-name Write output to a file named as the remote file

--remote-name-all Use the remote file name for all URLs

-R, --remote-time Set the remote file's time on the local output

-X, --request COMMAND Specify request command to use

--resolve HOST:PORT:ADDRESS Force resolve of HOST:PORT to ADDRESS

--retry NUM Retry request NUM times if transient problems occur

--retry-delay SECONDS When retrying, wait this many seconds between each

--retry-max-time SECONDS Retry only within this period

--sasl-ir Enable initial response in SASL authentication

-S, --show-error Show error. With -s, make curl show errors when they occur

-s, --silent Silent mode. Don't output anything

--socks4 HOST[:PORT] SOCKS4 proxy on given host + port

--socks4a HOST[:PORT] SOCKS4a proxy on given host + port

--socks5 HOST[:PORT] SOCKS5 proxy on given host + port

--socks5-hostname HOST[:PORT] SOCKS5 proxy, pass host name to proxy

-Y, --speed-limit RATE Stop transfers below speed-limit for 'speed-time' secs

-y, --speed-time SECONDS Time for trig speed-limit abort. Defaults to 30

--ssl Try SSL/TLS (FTP, IMAP, POP3, SMTP)

--ssl-reqd Require SSL/TLS (FTP, IMAP, POP3, SMTP)

-2, --sslv2 Use SSLv2 (SSL)

-3, --sslv3 Use SSLv3 (SSL)

--ssl-allow-beast Allow security flaw to improve interop (SSL)

--stderr FILE Where to redirect stderr. - means stdout

--tcp-nodelay Use the TCP_NODELAY option

-t, --telnet-option OPT=VAL Set telnet option

--tftp-blksize VALUE Set TFTP BLKSIZE option (must be >512)

-z, --time-cond TIME Transfer based on a time condition

-1, --tlsv1 Use => TLSv1 (SSL)

--tlsv1.0 Use TLSv1.0 (SSL)

--tlsv1.1 Use TLSv1.1 (SSL)

--tlsv1.2 Use TLSv1.2 (SSL)

--trace FILE Write a debug trace to the given file

--trace-ascii FILE Like --trace but without the hex output

--trace-time Add time stamps to trace/verbose output

--tr-encoding Request compressed transfer encoding (H)

-T, --upload-file FILE Transfer FILE to destination

--url URL URL to work with

-B, --use-ascii Use ASCII/text transfer

-u, --user USER[:PASSWORD][;OPTIONS] Server user, password and login options

--tlsuser USER TLS username

--tlspassword STRING TLS password

--tlsauthtype STRING TLS authentication type (default SRP)

**-A, --user-agent STRING User-Agent to send to server (H)**

-v, --verbose Make the operation more talkative

-V, --version Show version number and quit

-w, --write-out FORMAT What to output after completion

--xattr Store metadata in extended file attributes

-q If used as the first parameter disables .curlrc

- a/--append 上传文件时，附加到目标文件

-A/--user-agent <string> 设置用户代理发送给服务器

- anyauth 可以使用“任何”身份验证方法

-b/--cookie <name=string/file> cookie字符串或文件读取位置

- basic 使用HTTP基本验证

-B/--use-ascii 使用ASCII /文本传输

-c/--cookie-jar <file> 操作结束后把cookie写入到这个文件中

-C/--continue-at <offset> 断点续转

-d/--data <data> HTTP POST方式传送数据

--data-ascii <data> 以ascii的方式post数据

--data-binary <data> 以二进制的方式post数据

--negotiate 使用HTTP身份验证

--digest 使用数字身份验证

--disable-eprt 禁止使用EPRT或LPRT

--disable-epsv 禁止使用EPSV

-D/--dump-header <file> 把header信息写入到该文件中

--egd-file <file> 为随机数据(SSL)设置EGD socket路径

--tcp-nodelay 使用TCP_NODELAY选项

-e/--referer 来源网址

-E/--cert <cert[:passwd]> 客户端证书文件和密码 (SSL)

--cert-type <type> 证书文件类型 (DER/PEM/ENG) (SSL)

--key <key> 私钥文件名 (SSL)

--key-type <type> 私钥文件类型 (DER/PEM/ENG) (SSL)

--pass <pass> 私钥密码 (SSL)

--engine <eng> 加密引擎使用 (SSL). "--engine list" for list

--cacert <file> CA证书 (SSL)

--capath <directory> CA目录 (made using c_rehash) to verify peer against (SSL)

--ciphers <list> SSL密码

--compressed 要求返回是压缩的形势 (using deflate or gzip)

--connect-timeout <seconds> 设置最大请求时间

--create-dirs 建立本地目录的目录层次结构

--crlf 上传是把LF转变成CRLF

-f/--fail 连接失败时不显示http错误

--ftp-create-dirs 如果远程目录不存在，创建远程目录

--ftp-method [multicwd/nocwd/singlecwd] 控制CWD的使用

--ftp-pasv 使用 PASV/EPSV 代替端口

--ftp-skip-pasv-ip 使用PASV的时候,忽略该IP地址

--ftp-ssl 尝试用 SSL/TLS 来进行ftp数据传输

--ftp-ssl-reqd 要求用 SSL/TLS 来进行ftp数据传输

-F/--form <name=content> 模拟http表单提交数据

-form-string <name=string> 模拟http表单提交数据

-g/--globoff 禁用网址序列和范围使用{}和[]

-G/--get 以get的方式来发送数据

-h/--help 帮助

-H/--header <line>自定义头信息传递给服务器

--ignore-content-length 忽略的HTTP头信息的长度

-i/--include 输出时包括protocol头信息

-I/--head 只显示文档信息

从文件中读取-j/--junk-session-cookies忽略会话Cookie

- 界面<interface>指定网络接口/地址使用

- krb4 <级别>启用与指定的安全级别krb4

-j/--junk-session-cookies 读取文件进忽略session cookie

--interface <interface> 使用指定网络接口/地址

--krb4 <level> 使用指定安全级别的krb4

-k/--insecure 允许不使用证书到SSL站点

-K/--config 指定的配置文件读取

-l/--list-only 列出ftp目录下的文件名称

--limit-rate <rate> 设置传输速度

--local-port<NUM> 强制使用本地端口号

-m/--max-time <seconds> 设置最大传输时间

--max-redirs <num> 设置最大读取的目录数

--max-filesize <bytes> 设置最大下载的文件总量

-M/--manual 显示全手动

-n/--netrc 从netrc文件中读取用户名和密码

--netrc-optional 使用 .netrc 或者 URL来覆盖-n

--ntlm 使用 HTTP NTLM 身份验证

-N/--no-buffer 禁用缓冲输出

-o/--output 把输出写到该文件中

-O/--remote-name 把输出写到该文件中，保留远程文件的文件名

-p/--proxytunnel 使用HTTP代理

--proxy-anyauth 选择任一代理身份验证方法

--proxy-basic 在代理上使用基本身份验证

--proxy-digest 在代理上使用数字身份验证

--proxy-ntlm 在代理上使用ntlm身份验证

-P/--ftp-port <address> 使用端口地址，而不是使用PASV

-Q/--quote <cmd>文件传输前，发送命令到服务器

-r/--range <range>检索来自HTTP/1.1或FTP服务器字节范围

--range-file 读取（SSL）的随机文件

-R/--remote-time 在本地生成文件时，保留远程文件时间

--retry <num> 传输出现问题时，重试的次数

--retry-delay <seconds> 传输出现问题时，设置重试间隔时间

--retry-max-time <seconds> 传输出现问题时，设置最大重试时间

-s/--silent静音模式。不输出任何东西

-S/--show-error 显示错误

--socks4 <host[:port]> 用socks4代理给定主机和端口

--socks5 <host[:port]> 用socks5代理给定主机和端口

--stderr <file>

-t/--telnet-option <OPT=val> Telnet选项设置

--trace <file> 对指定文件进行debug

--trace-ascii <file> Like --跟踪但没有hex输出

--trace-time 跟踪/详细输出时，添加时间戳

-T/--upload-file <file> 上传文件

--url <URL> Spet URL to work with

-u/--user <user[:password]>设置服务器的用户和密码

-U/--proxy-user <user[:password]>设置代理用户名和密码

-v/--verbose

-V/--version 显示版本信息

-w/--write-out [format]什么输出完成后

-x/--proxy <host[:port]>在给定的端口上使用HTTP代理

-X/--request <command>指定什么命令

-y/--speed-time 放弃限速所要的时间。默认为30

-Y/--speed-limit 停止传输速度的限制，速度时间'秒

-z/--time-cond 传送时间设置

-0/--http1.0 使用HTTP 1.0

-1/--tlsv1 使用TLSv1（SSL）

-2/--sslv2 使用SSLv2的（SSL）

-3/--sslv3 使用的SSLv3（SSL）

--3p-quote like -Q for the source URL for 3rd party transfer

--3p-url 使用url，进行第三方传送

--3p-user 使用用户名和密码，进行第三方传送

-4/--ipv4 使用IP4

-6/--ipv6 使用IP6

-#/--progress-bar 用进度条显示当前的传送状态

参考：[https://cizixs.com/2017/04/11/use-curl-to-analyze-request/](https://cizixs.com/2017/04/11/use-curl-to-analyze-request/)