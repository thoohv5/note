---
title: EXISTS & IN
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: reference
status: complete
---

## EXISTS & IN

## 概述

### EXISTS场景

```sql
select * from A where id in(select id from B)
```

**以上查询使用了in语句,in()只执行一次,它查出B表中的所有id字段并缓存起来.之后,检查A表的id是否与B表中的id相等,如果相等则将A表的记录加入结果集中,直到遍历完A表的所有记录.**

- 它的查询过程类似于以下过程
    
    ```java
    List resultSet=[];
    Array A=(select * from A);
    Array B=(select id from B);
    
    for(int i=0;i<A.length;i++) {
    	for(int j=0;j<B.length;j++) {
    		if(A[i].id==B[j].id) {
    			resultSet.add(A[i]);
    			break;
    		}
    	}
    }
    return resultSet;
    ```
    
    可以看出,当B表数据较大时不适合使用in(),因为它会B表数据全部遍历一次.
    如:A表有10000条记录,B表有1000000条记录,那么最多有可能遍历10000*1000000次,效率很差.
    再如:A表有10000条记录,B表有100条记录,那么最多有可能遍历10000*100次,遍历次数大大减少,效率大大提升.
    
    结论:in()适合B表比A表数据小的情况
    

### IN场景

```sql
select a.* from A a where exists(select 1 from B b where [a.id=b.id](http://a.id=b.id/))
```

**以上查询使用了exists语句,exists()会执行A.length次,它并不缓存exists()结果集,因为exists()结果集的内容并不重要,重要的是结果集中是否有记录,如果有则返回true,没有则返回false.**

- 它的查询过程类似于以下过程
    
    ```java
    List resultSet=[];
    Array A=(select * from A)
    
    for(int i=0;i<A.length;i++) {
    	if(exists(A[i].id) {    //执行select 1 from B b where b.id=a.id是否有记录返回
    		resultSet.add(A[i]);
    	}
    }
    return resultSet;
    ```
    
    当B表比A表数据大时适合使用exists(),因为它没有那么遍历操作,只需要再执行一次查询就行.
    如:A表有10000条记录,B表有1000000条记录,那么exists()会执行10000次去判断A表中的id是否与B表中的id相等.
    如:A表有10000条记录,B表有100000000条记录,那么exists()还是执行10000次,因为它只执行A.length次,可见B表数据越多,越适合exists()发挥效果.
    再如:A表有10000条记录,B表有100条记录,那么exists()还是执行10000次,还不如使用in()遍历10000*100次,因为in()是在内存里遍历比较,而exists()需要查询数据库,我们都知道查询数据库所消耗的性能更高,而内存比较很快.
    
    结论:exists()适合B表比A表数据大的情况
    

## MySQL 5.7

EXISTS与IN的使用效率的问题，通常情况下采用exists要比in效率高，因为IN不走索引，但要看实际情况具体使用：

IN适合于外表大而内表小的情况；EXISTS适合于外表小而内表大的情况。

## 附录

[SQL中IN和EXISTS用法的区别](https://www.cnblogs.com/clarke157/p/7912871.html)