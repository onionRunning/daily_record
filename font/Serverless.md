### Serverless
#### 背景

提供无需关心底层基础设施，即可编写和部署代码的无服务开发体验。同时提供资源管理、自动伸缩、统计分析等能力，让广大开发者可以节省运维成本，真正做到“按量付费”的同时，也无需花费精力处理日志收集，异常统计等任务

Serverless = FaaS + BaaS
#### FaaS 函数即服务: Function as a Service 

用户抛弃对服务器的配置和管理只用编写核心业务代码, 交由平台完成 部署调度,流量分发 弹性伸缩。

#### BaaS 后端即服务: Backend as a Service

这里的后端指的是, 各种云产品和云服务, 对象储存 消息队列, 云数据库, 云缓存！

优势:

- 解决传统服务器模式的繁琐步骤: 环境，数据库，储存等准备工作,各种扩容已经复杂的运维工作, 利用serverless 专注于业务相关核心代码更快完成业务上线和迭代

- 通过函数的调用次数和执行时间来统计费用保证最大化的利用费用支出

- 无需运维人员再投入到基础设施中去了，而开发人员也可以全面关注业务系统的开发


#### 参考文章

1. [github](https://github.com/serverless/serverless/blob/master/README_CN.md)
2. [serverless 官网](https://www.serverless.com/cn/)
3. [serverless 中文网](https://serverlesscloud.cn/)
4. [美团Serverless平台Nest的探索与实践](https://tech.meituan.com/2021/04/21/nest-serverless.html)