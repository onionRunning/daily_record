# ci 优化

## 背景

```md
1. 日常被diss 前端ci居然跑了 8,9,10 分钟了 严重阻塞了ci的进度，强烈要求优化！
```

gitlab ci: .yml 执行 shell文件 ， 执行docker build 等更新操作！

## 分析部署脚本实现

```md
1. 创建临时文件夹 + 复制本项目所有代码(除了 node_nodules) + 压缩文件夹
 
2. docker build: copy + yarn + yarn build  (build)

3. docker tag  + docker login + docker push + kubectl update


> 耗时较长 肯定要去查看那些耗时较长于是打印log： 

第一阶段: copy: 20210818-18:52:31
第二阶段: copy: 20210818-18:58:42
第三阶段: copy: 20210818-19:01:38
```

## 如何优化

```md
可以发现问题来了: 第二阶段 + 第三阶段 时间过长, 这也和我们把很多耗时的工作都丢到, docker 相关的操作

明显的就是三个工作耗时: yarn + yarn build + docker push

当然: runner性能跑满，服务器压力过高，导致整体变慢问题(服务器的原因也有)

> 优化：缓存(cache)
```

### 优化

#### 1. 利用 依赖前置 Docker cache

```Dockerfile

# 修改之前

FROM node:12.16.3-alpine

ENV PROJECT_NAME app-foxtable-front

COPY $PROJECT_NAME.tar.gz ./

RUN tar xzvf $PROJECT_NAME.tar.gz -C ./ && cd $PROJECT_NAME && npm config set registry https://registry.npm.taobao.org  && yarn --registry https://registry.npm.taobao.org/ && yarn build 

CMD cd $PROJECT_NAME && yarn server:start

# ----------------------------------------------------------------------------------------------------------------

# 修改之后

FROM node:12.16.3-alpine

COPY ./package.json ./yarn.lock /

RUN yarn config set registry 'https://registry.npm.taobao.org/' &&  yarn

# ps. 这里是存在docker cache 事实上我们的 package.json 不是经常变化的所有这一块完全可以前置, 如果发生了变化 才会重新触发: yarn 过程

ADD . /

RUN yarn build 

CMD yarn server:start

```

> 这个地方修改了只要 package.json 不发生改变, docker build 由于分层就可以吧 node_modules 其中一层不变的话, docker build  / docker push 的时间也会快很多！ 一举多得

#### 2. 清理无用镜像

```sh
# 删除相关的镜像
docker rmi --force `docker images | grep ${remote_store} | awk '{print $0}'`  

# 删除 Tag 为 <none> 的镜像
docker rmi --force `docker images | grep '<none>' | awk '{print $3}'` 
```

#### 3. 减少无用步骤

```md
其实之前做了很多无用的步骤, 包括压缩 + 解压缩 等,其实去除无用的步骤可以让自己的逻辑更加清晰!
```

> 关于 Gitlab CI cache 也是可以的但是优化的话 只是缓存的node_modules 避免了 重复yarn 其他过程也是一样的, 并且 没有docker cache的优势 没有前者好!

#### 结果

> 下面哪个就是未优化之前的, 上面是优化后的! 那么还有继续优化的空间么!往下看

![wecom-temp-80762cfe7f2a2e7d97ed210909a9c635.png](https://i.loli.net/2021/08/27/mDVn4Qw3I6gTxGh.png)

> 可以看到这是跑yarn build 耗用的时间居然是: 1min42s， 然后自己本地 build 也就20s的样子！😄

1. 前端可以考虑继续优化打包时长, 效果估计不明显

2. 整个gitlab runner也可以考虑优化!

![wecom-temp-cd718253d81cbdfaaa922efc91b7aeec.png](https://i.loli.net/2021/08/27/4NXtxg5bE9eszuK.png)

### Dockerfile COPY vs ADD

```md

COPY 源路径 目标路径 

COPY package.json /home  (目标路径会自行创建<如果不存在的话>)

ADD 源路径 目标路径  

ADD 具有一样的复制功能,  区别: 镜像构建会造成缓存失效, 能够自动解压缩: gzip

ADD XX.tar.gz /    ------->    会复制过来并且自动解压
```

## 参考文章

1. [前端CI中的性能优化](https://banyudu.com/posts/04.%E5%89%8D%E7%AB%AFCI%E4%B8%AD%E7%9A%84%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96.2c2f97)
2. [Docker Dockerfile](https://www.runoob.com/docker/docker-dockerfile.html)