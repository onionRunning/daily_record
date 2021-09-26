# 搭建

巴拉巴拉, 官网上就有,修改配置 ...

## 设置权限

```md
1. 登录用户才可以发包
```

## 注册用户

```md
npm adduser --registry http://dev.moumoux.com:9088

```

## publish

```md
npm login / npm login --registry http://dev.moumoux.com:9088

npm publish --registry http://dev.moumoux.com:9088

or 
切换源直接: npm push

```

## 安装用法

- 方式1

```md
yarn add 5e-hooks --registry http://dev.moumoux.com:9088
```

- 方式2

```md
直接用nrm来管理你npm源, 手动切换 
nrm add xx http://dev.moumoux.com:9088/
nrm ls
nrm use xx

然后直接: yarn add 5e-hooks
```

> ps. 私有npm 内部有代理，现在默认指向的是淘宝镜像

## 撤销发的包

```md
1. npm unpublish packagename --force --registry http://dev.moumoux.com:9088 (仓库地址)
```

## problem

> 发包不成功

```md
1. 是不是包体积过大
2. 是不是没登录
3. 包名...
```

## 参考文章

1. [github](https://github.com/verdaccio/verdaccio)
2. [npm 撤销发布包](https://www.cnblogs.com/penghuwan/p/6973702.html#_label0)
3. [nrm 管理npm源](https://www.cnblogs.com/Jimc/p/10280774.html)
