# configure

**Nginx+Chrome 修改js文件不生效**

```bash
sendfile off;
```

命令说明

```bash
Enables or disables the use of `sendfile()`.

Starting from nginx 0.8.12 and FreeBSD 5.2.1, [aio](http://nginx.org/en/docs/http/ngx_http_core_module.html#aio) can be used to pre-load data for `sendfile()`:

> location /video/ {
    sendfile       on;
    tcp_nopush     on;
    aio            on;
}

In this configuration, `sendfile()` is called with the `SF_NODISKIO` flag which causes it not to block on disk I/O, but, instead, report back that the data are not in memory. nginx then initiates an asynchronous data load by reading one byte. On the first read, the FreeBSD kernel loads the first 128K bytes of a file into memory, although next reads will only load data in 16K chunks. This can be changed using the [read_ahead](http://nginx.org/en/docs/http/ngx_http_core_module.html#read_ahead) directive.
```