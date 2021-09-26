# 前端开发者在运维部署方面也需要了解一些简单的知识

背景: 我们会常常听到几个常见的概念: k8s, docker, Dockerfile , 容器 , 镜像, 仓库 ..., 本文的目标就是针对这些基础的知识做简单的讲解, 自己也能够通过这种方式部署一个简单的应用

## Docker

基于go语言的开源的容器引擎,遵循Apache2.0协议开源, 可以 ***打包他们的应用以及依赖包到一个轻量级、可移植的容器中*** ，然后发布到任何流行的 Linux 机器上。

Docker 有以下基本概念:

- 镜像Image: 相当于静态的文件系统
- 容器Container: 容器是镜像运行的实体
- 仓库: 镜像管理中心

***创建一个镜像***

### Dockerfile

> 用于构建镜像的文本文件(配置): 指令 + 说明

```md
FROM
就是需要依赖的基础镜像比如: FROM node:9.0, 依赖基础镜像后才有相应的功能


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

### 开始打镜像

docker build -f xx -t yy .

```md
参数挺多的 比较关注的是 -f 与 -t

-f: 代表着使用的 Dockerfile路径
-t: 代表镜像的名字和标签
```

ps. 镜像是分层的我们可以一直往上去堆叠我们的最终环境!可以去按照自己想要的去定制,也可以pull 一些已经搞好的镜像!

***启动容器: 运行镜像***

通过以上步骤就生成了个镜像, 然后我们可以开始运行镜像

docker run --name xx -p xx:yy aa -d

```md
--name xx 是你命名的容器名

-p 是映射的端口    主机端口:容器端口 

aa 镜像名字

-d 默认后台执行(如果自己调试的话就不加)
```

***推镜像到仓库里面去***

一般的过程是你先登录了, 然后docker push 你的镜像到仓库, 当然了为了保证安全性 需要每次推的时候加上登录的账号和密码, 或者你也可以配置免密码直接就可以push到对应的仓库!

> 以下是需要账号和密码的方式,更加安全

```sh
echo "开始登录docker 对应的仓库地址"
docker login --username=xxx --password=yyy ${remote_store}

echo "开始发布相应的镜像"
docker push ${remote_store}:${commit_short_id}
```

搭配ci的流程是通过环境的区分知道了需要推哪个镜像到对应的仓库

### 常见的docker命令

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

ps. 注意无论在哪台机器上我们最好在生成镜像时需要删除被覆盖的遗留的镜像, 当我们生成同一个名字的镜像时被覆盖时就会产生多余的镜像然后占好多内存, 构建次数 * 项目个数 * 镜像大小 (反正会慢慢堆积)!!!

- 查看镜像

```sh
docker images | grep xx
```

- 查看容器信息

```sh
docker ps | grep xx
```

- 进入容器查看目录信息等

```sh
docker exec -it containerId /bin/bash
```

### 我们现有的部署方式

容器内部启动我们的服务

```md
- 通过node的方式启动服务
- 通过nginx 挂载静资源
```

- 把我们的项目进行打包

> **下面就介绍以下俩种方式前端需要打成的镜像**

## k8s(Kubernetes)

到了这一步我们前端开发者就已经差不多够了, 我们用镜像部署的方式代替了传统的部署方式,服务器上我们甚至不用装node mysql ... 等环境依赖, 只要有docker就行 我们把我们的镜像 丢到相应的服务器上, 然后 docker run 一下我们的镜像 然后配置好我们的域名..., 但是其实这种与传统的区别在与各自环境的依赖打到了镜像里面去了, 所以需要一个统一管理的东西去比编排我们的容器然后还要有更多丰富的功能 

> **一个用于管理编排容器的工具 ,用于管理云平台中多个主机上的容器化的应用，Kubernetes的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes提供了应用部署，规划，更新，维护的一种机制。**

写到这里我们了解到了前端差不多需要了解的知识, 并且我们也能简单的部署!

参考文章:

1. [菜鸟教程](https://www.runoob.com/docker/docker-architecture.html)
2. [Docker从入门到上瘾](https://juejin.cn/post/6924523363956162574#heading-24)
3. [k8s 中文社区](https://www.kubernetes.org.cn/k8s)