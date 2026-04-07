# RESTful

> 表现层状态转化(Representational State Transfer)
> 

```bash
GET:    用来获取资源
POST:   用来新建资源
PUT:    用来更新全部资源
PATCH:  用来更新指定资源
DELETE: 用来删除资源
```

总述：

- 每一个URI代表一种资源
- 客户端和服务器之间，传递这种资源的某种表现层
- 客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"

误区：

- URI包含动词
- URI中加入版本号

## 版本控制策略

1. The Knot：无版本
2. Point-to-Point：点对点，即平台的API版本自带版本号
3. Compatible Versioning：兼容性版本控制

### 实操

1. URI， 即在URI中直接标记使用的是哪个版本，无版本号URI默认使用最新版本
2. Request Parameter， 即在每个请求后添加一个version参数，表示请求的是哪个版本。
3. Mdedia Type， 即在HTTP请求的header中使用Media Type标记该请求想获取的资源， 同样的可以不设置或设置通用的Media Type表示最新版本的API。

# 附录

[理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)

[RESTful API版本控制策略-pudn.com](https://www.pudn.com/news/628f839fbf399b7f351ea3d0.html)