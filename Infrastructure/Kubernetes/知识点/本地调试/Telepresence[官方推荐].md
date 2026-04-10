---
title: Telepresence[官方推荐]
date: 2026-04-07
tags: [基础设施, K8s]
type: note
status: complete
---

# Telepresence[官方推荐]

# 安装Client

```bash
# 1. Download the binary.
sudo curl -fL https://github.com/telepresenceio/telepresence/releases/latest/download/telepresence-darwin-amd64 -o /usr/local/bin/telepresence

# 2. Make the binary executable:
sudo chmod a+x /usr/local/bin/telepresence
```

# 安装**Traffic Manager**

```bash
mkdir -p "$HOME/Library/Application\ Support/telepresence"
cat > "$HOME/Library/Application\ Support/telepresence/config.yml" <<EOF
timeouts:
  helm: 120s
  clusterConnect: 60s
EOF
```

```bash
telepresence helm install
```

```bash
helm repo add datawire https://app.getambassador.io
helm repo update
helm install traffic-manager datawire/telepresence --namespace ambassador --create-namespace
```

# 卸载

```bash
telepresence uninstall --everything
```

# 使用

```bash
telepresence connect
```

## 替换

```bash
Replace
```

## 拦截

### 全局拦截（Global intercept）

将访问k8s中某个service的流量全部拦截，并转发到本地。

```bash
telepresence intercept <service-name> --port <local-port>[:<remote-port>] --http-match=all --env-file <path-to-env-file> [--namespace 可选]
```

### 个人拦截（Personal intercept）

有选择性地仅拦截某个service的部分流量，而不会干扰其余流量。

```bash
--http-match=key=value 基于请求头识别请求是否需要拦截转发
--http-path-equal <path> 基于请求路径
--http-path-prefix <prefix>  基于请求路径前缀
--http-path-regex <regex> 基于请求路径是否匹配给定的正则表达式
```

## **窃听**

## **摄取**