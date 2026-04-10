---
title: Debezium
date: 2026-04-07
tags: [基础设施, CDC]
type: guide
status: complete
---

# Debezium

**读法：得-比-自-厄姆**

# 组合 Kafka + Kafka Connect + Debezium

## docker-compose.yml

```yaml
services:
  kafka:
    image: apache/kafka:4.0.0
    container_name: kafka
    environment:
      # KRaft 模式参数
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_NODE_ID: 1
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,CONTROLLER:PLAINTEXT
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_CLUSTER_ID: "N4dL5Yr7RS6NQhbr-LJiqg"  # 这里需要提前生成，方法见下面
      KAFKA_CONFLUENT_SUPPORT_METRICS_ENABLE: "false"
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "true"
    ports:
      - "9092:9092"
      - "9093:9093"
    healthcheck:
      test: ["CMD", "kafka-broker-api-versions", "--bootstrap-server", "localhost:9092"]
      interval: 10s
      retries: 10
      timeout: 10s

  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_USER: debezium
      MYSQL_PASSWORD: dbz
      MYSQL_DATABASE: testdb
    ports:
      - "3306:3306"
    command:
      [
        "--character-set-server=utf8mb4",
        "--collation-server=utf8mb4_unicode_ci",
        "--server-id=223344",
        "--log-bin=mysql-bin",
        "--binlog-format=ROW",
        "--gtid-mode=ON",
        "--enforce-gtid-consistency=ON",
        "--log-slave-updates=ON",
        "--binlog-row-image=FULL"
      ]
    healthcheck:
      test: [ "CMD", "mysqladmin", "ping", "-uroot", "-prootpassword" ]
      interval: 10s
      timeout: 5s
      retries: 5

  connect:
    image: debezium/connect:2.2
    container_name: connect
    depends_on:
      - kafka
      - mysql
    ports:
      - "8083:8083"
    environment:
      BOOTSTRAP_SERVERS: kafka:9092
      GROUP_ID: 1
      CONFIG_STORAGE_TOPIC: my_connect_configs
      OFFSET_STORAGE_TOPIC: my_connect_offsets
      STATUS_STORAGE_TOPIC: my_connect_statuses
      KEY_CONVERTER_SCHEMAS_ENABLE: "false"
      VALUE_CONVERTER_SCHEMAS_ENABLE: "false"
      KEY_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      VALUE_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      INTERNAL_KEY_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      INTERNAL_VALUE_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      CONNECT_REST_ADVERTISED_HOST_NAME: connect

      # 添加这两个环境变量，给所有 Connector 默认生效
      CONNECT_SCHEMA_HISTORY_INTERNAL_KAFKA_TOPIC: "schema-changes.testdb"
      CONNECT_SCHEMA_HISTORY_INTERNAL_KAFKA_BOOTSTRAP_SERVERS: "kafka:9092"
      CONNECT_INTERNAL_KEY_CONVERTER_SCHEMA_REGISTRY_TOPIC: "schema-changes.testdb"
```

## 配置 MySQL 账号权限

```yaml
docker exec -it mysql mysql -uroot -prootpassword

-- 授权 debezium 用户
GRANT SELECT, RELOAD, SHOW DATABASES, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'debezium'@'%';
FLUSH PRIVILEGES;
```

## 注册CDC任务

```yaml
curl -X POST http://localhost:8083/connectors -H 'Content-Type: application/json' -d '{
  "name": "mysql-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "database.hostname": "mysql",
    "database.port": "3306",
    "database.user": "debezium",
    "database.password": "dbz",
    "database.server.id": "223344",
    "database.server.name": "test-mysql",
    "database.include.list": "testdb",
    "topic.prefix": "test-mysql",
    "table.include.list": "testdb.users",
    "schema.history.internal.kafka.bootstrap.servers": "kafka:9092",
    "schema.history.internal.kafka.topic": "schema-changes.testdb",
    "include.schema.changes": "true",
    "database.history.skip.unparseable.ddl": "true",
    "database.allowPublicKeyRetrieval": "true",
    "database.useSSL": "false",
    "snapshot.mode": "initial"
  }
}'

curl -s http://localhost:8083/connectors | jq

curl -X DELETE http://localhost:8083/connectors/mysql-connector

curl -s http://localhost:8083/connectors/mysql-connector/status | jq
```

## 验证

```yaml
docker exec -it mysql mysql -uroot -prootpassword testdb

CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100),
  email VARCHAR(100)
);

INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');
```

```yaml
docker exec -it kafka /opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

docker exec -it kafka /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test-mysql.testdb.users --from-beginning

```

```yaml
curl -x "" http://localhost:8083/connectors/mysql-connector/status
```