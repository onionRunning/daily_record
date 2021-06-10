### Serverless
#### 背景

提供无需关心底层基础设施，即可编写和部署代码的无服务开发体验。用户无须关心软件应用运行涉及的底层服务器的状态、资源（比如 CPU、内存、磁盘及网络）及数量。软件应用正常运行所需要的计算资源由底层的云计算平台动态提供。

FaaS & BaaS

```
FaaS 函数即服务: Function as a Service 
用户抛弃对服务器的配置和管理只用编写核心业务代码, 交由平台完成 部署调度,流量分发 弹性伸缩。是一种事件驱动由消息触发的函数服务


BaaS 后端即服务: Backend as a Service
这里的后端指的是, 各种云产品和云服务, 对象储存 消息队列, 云数据库, 云缓存！
```

#### 前端 BFF

backend for frontend: 服务于前端的后端是一个中间服务,上游是前端 下游是后端, 一般是由前端进行编写用node.js进行开发!
向上给前端提供接口服务,向下直接调用服务端的rpc接口 或转发的http接口, 这一层用于接口聚合,数据处理等逻辑！比如比较出名的graphql作为bff层！后端可以专注于微服务逻辑, 前端可以承担部分后端的职能!

缺点也比较明显: 链路复杂需要专门的人去维护这个中间服务, 流程繁琐复杂, 并且这个服务承担大量的流量有并发的压力, 重复开发增加了开发成本!

#### 优势:

- 解决传统服务器模式的繁琐步骤: 环境，数据库，储存等准备工作,各种扩容已经复杂的运维工作, 利用serverless 专注于业务相关核心代码更快完成业务上线和迭代

- 通过函数的调用次数和执行时间来统计费用保证最大化的利用费用支出

- 无需运维人员再投入到基础设施中去了，而开发人员也可以全面关注业务系统的开发


#### 

#### 参考文章

1. [github](https://github.com/serverless/serverless/blob/master/README_CN.md)
2. [serverless 官网](https://www.serverless.com/cn/)
3. [serverless 中文网](https://serverlesscloud.cn/)
4. [美团Serverless平台Nest的探索与实践](https://tech.meituan.com/2021/04/21/nest-serverless.html)