---
title: Floor and Ceiling
date: 2026-04-07
  - 算法
type: note
status: complete
---

## Floor and Ceiling

四舍五入取整、向上取整、向下取整、小数截取

### Golang

```jsx
## 四舍五入取整
fmt.Println(int(math.Floor(float64(8)/5+0.5))) // 2

## 向上取整
fmt.Println(math.Ceil(float64(8)/5)) // 2
fmt.Println((8+5-1)/5) // 2

## 向下取整，默认
fmt.Println(8/5) // 1
fmt.Println(float64(8)/5) // 1.6
fmt.Println(math.Floor(float64(8)/5)) // 1
```

PHP

```jsx
## 四舍五入取整
echo round(8/5);

## 向上取整
echo ceil(8/5);

## 向下取整
echo floor(8/5);
```