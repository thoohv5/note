# DB的编码

```
# 创建数据库执行编码
CREATE DATABASE your_database_name ENCODING 'UTF-8';

# DB
SELECT datname, pg_encoding_to_char(encoding) FROM pg_database;

```

## 服务器编码

```
show server_encoding;

```

## 客户端编码

```
show client_encoding;

```

## 本地环境编码

```
locale

```