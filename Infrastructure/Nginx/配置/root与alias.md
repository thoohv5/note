---
title: root与alias
date: 2026-04-07
tags: [基础设施, Nginx, 配置]
type: note
status: complete
---

## root与alias

- alias是目录别名的定义，如果uri以“/”结束，alias必须要用“/”结束，否则403

```
location /img/ {
    alias /var/www/;  # 实际路径是/var/www/
}

location ~* /img/(.+\.(gif|png|jpeg)) { # location用正则表达式则必须包含捕获语句（也就是括号()）
    alias /usr/local/images/$1;         # alias则必须要引用这些捕获值
}

（1）使用alias时，目录名后面一定要加"/"。
（2）alias在使用正则匹配时，必须捕捉要匹配的内容并在指定的内容处使用。
（3）alias只能位于location块中。（root可以不放在location中）
```

- root是最上层目录的定义，文件的绝对路径等于 root+location

```
location /img/ {
    root /var/www;    # 实际路径是/var/www/img/
}
```

- proxy_pass是反向代理配置，用“/”结束不附加location配置路径

```
location /test/  {
     proxy_pass http://127.0.0.1:8080; # 实际将请求转发到http://127.0.0.1:8080/test/
}

location   /test/  {
     proxy_pass http://127.0.0.1:8080/; # 实际将请求转发到http://127.0.0.1:8080/
}

```