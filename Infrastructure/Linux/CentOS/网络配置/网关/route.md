---
title: route
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: complete
---

## route

### route

show / manipulate the IP routing table

查看

```jsx
route -n
```

### 添加

```jsx
route add [-net|-host] target [network Num][[default]gw Gw][dev If]
```

### 删除

```jsx
route del [-net|-host] target [network Num][gw Gw][dev If]
```

### ip route

**routing table management**

### 查看

```jsx
ip route [show]
```

### 添加

```jsx
ip route add TARGET via GW [dev IFACE] [src SOURCE_IP] (dev表示流出的接口，via:经由下一跳）
```

### 删除

```jsx
ip route del TARGET
```

### 清空

```jsx
ip route flush
```