### 前端开发者在运维部署方面也需要了解一些简单的知识

背景: 我们会常常听到几个常见的概念: k8s, docker, Dockerfile , 容器 , 镜像, 仓库 ..., 本文的目标就是针对这些基础的知识做一定讲解然后, 自己也能够通过这种方式部署一个简单的应用

### Docker

基于go语言的开源的容器引擎,遵循Apache2.0协议开源, 可以 ***打包他们的应用以及依赖包到一个轻量级、可移植的容器中*** ，然后发布到任何流行的 Linux 机器上。

Docker 分为3个基本概念:

- 镜像Image: 相当于静态的文件系统
- 容器Container: 容器是镜像运行的实体
- 仓库: 镜像管理中心

***创建一个镜像***

#### Dockerfile

> 用于构建镜像的文本文件(配置): 指令 + 说明

```
FROM
就是需要依赖的基础镜像比如: FROM node:9.0


RUN
基本上就是你执行的shell命令比如: RUN tar xzvf $PROJECT_NAME.tar.gz -C ./ && cd $PROJECT_NAME

CMD
当镜像开始run起来的时候会触发: CMD cd $PROJECT_NAME && npm run server:start


COPY
就是复制文件

ENV
这个是定义变量的: ENV XX 1.0.0 然后用的时候就 $XX 代表的就是 1.0.0
 
EXPOSE
镜像启动后对外暴露的端口号

```

#### 开始打镜像

docker build -f xx -t yy .

```
参数挺多的 比较关注的是 -f 与 -t

-f: 代表着使用的 Dockerfile路径
-t: 代表镜像的名字和标签
```

***启动容器: 运行镜像***

通过以上步骤就生成了个镜像, 然后我们可以开始运行镜像

docker run --name xx -p xx:yy aa -d

```
--name xx 是你命名的容器名

-p 是映射的端口    主机端口:容器端口 

aa 镜像名字

-d 默认后台执行
```

***推镜像到仓库里面去***

一般的过程是你先登录了, 然后docker push 你的镜像到仓库

```sh
echo "开始登录docker 对应的仓库地址"
docker login --username=xxx --password=yyy ${remote_store}

echo "开始发布相应的镜像"
docker push ${remote_store}:${commit_short_id}
```

搭配ci的流程是通过环境的区分知道了需要推哪个镜像到对应的仓库

#### 常见的docker命令

- 删除容器

```sh
docker rm `docker ps -a -q` // 删除所有容器

docker rm containerId // 删除指定id容器
```

- 删除镜像

```sh
删除指定关键字的镜像
docker rmi --force `docker images | grep doss-api | awk '{print $3}'`   // doss-api关键字

docker rmi imageId // 删除指定镜像id
```

### k8s

用于管理编排容器的工具,


参考文章:

1. [菜鸟教程](https://www.runoob.com/docker/docker-architecture.html)