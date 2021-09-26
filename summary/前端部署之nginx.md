# 前端开发者在运维部署方面需要了解的nginx

## 背景

某次部署时然后需要用nginx 去部署, 然后就要去翻翻文档 然后一直忘记 一直看文档...

> 目标

本文旨在介绍nginx基本概念和用法,让大家能够对这个知识点有一定的了解,然后能够达到如何去用nginx部署项目 还有解决一些问题! 对了还有,当你忘记某些用法时, 过来看看哦，原来要这么写哈哈! 好了下面会从3个方面介绍:

- 了解
- 知道去用
- api介绍

## 基本概念

笔记强化记忆!

### 什么是nginx

官方介绍: 高性能的 web服务器 / 反向代理服务器, 同时也提供了IMAP/POP3/SMTP服务!

- web服务器:

#### nginx 可以用来做什么

#### 原理

一听就很高大上 nginx的实现原理, 有没有发现不知道从什么时候开始我们喜欢问各种原理,看来我们在api调用的层面又前进了一大步!

### 用法

**自行google/baidu 搜索安装按照教程装就可以了！**

当我们安装好了之后 我们执行: nginx -t , 看到以下log 说明已经装好了, 当然路径图有可能不一样 看你自己安装的路径!

```md
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful

这个时候我们就可以知道: /usr/local/etc/nginx/nginx.conf 这个文件就是我们的配置, 我们去修改 然后重新reload 就可以生效了!

```

### 实际项目集成

### 总结

参考链接:

1. [菜鸟教程](https://www.runoob.com/linux/nginx-install-setup.html)
2. [nginx 官方文档](http://nginx.org/en/docs/)
