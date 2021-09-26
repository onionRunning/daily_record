# webpack5 特性 Module Federation(1)

本文聊一聊webpack5 的一个特性 Module Federation, 之前了解微前端几种实现方式主要有以qiankun为代表的基座模式, 主应用 + 副应用的模式 还有一种就是基于webpack5 Module Federation 实现的去中心模式的方式,而去中心模式的实现就是依赖了 webpack5 的 MF 下面我们就介绍 Module Federation 到底是什么

我们如何做到代码复用:

- 抽离公共的utils/component
- 发布npm包

以上的两种方式: 第一种随着代码量的增加会越来月臃肿，然后我们就采用了第二种方式但是发现每次npm构建发包时, 我的依赖该包的项目就要update 然后webpack5 通过mf 就可以实现代码共享并且还可以单独部署他们!

Module Federation 是什么?

```md
mf motivation: 联邦模块

多个独立的构建可以形成一个应用程序。这些独立的构建不会相互依赖，因此可以单独开发和部署它们。
这通常被称为微前端，但并不仅限于此。
```

> ps. 可以把多个无相互依赖、单独部署的应用合并为一个, 把当前应用具有加载其它应用的能力

![示意图](https://hulaplanet.oss-cn-shenzhen.aliyuncs.com/test/mf1.png)

如图所示:

上图可以表明具有mf功能的每个应用都可以作为 HOST/REMOTE 两种角色: HOST 就是引用别人 REMOTE 就是被人引用， 并且每个节点的地位都是一样的也就是去中心！

> 写到这里我们可以发现这种方式其中一个特性: 具有动态修改 npm包的应用

## 简单配置说明

***上诉简要说明大致知道了mf 具有的特性, 后面会介绍项目中的配置**

```js

const ModuleFederationPlugin = require("webpack").container.ModuleFederationPlugin

plugins: [
    new ModuleFederationPlugin({  
      name: 'baseComponent',
      library: { type: 'var', name: 'baseComponent' },
      filename: 'base.js',
      remotes: {
        app_base2: "baseComponent2",
      },
      exposes: {
        './button': './src/pc/components/button',
      },
      shared: {}
    })
]

我们关注的有以上配置:

name: 'baseComponent'                   唯一字输出的模块名命名, 被远程引用时路径为${name}/${expose}
filename: 'base.js'                     构建输出的文件名
remotes: {
    // key: value
    key1: `${name}@http://localhost:9000/${filename}`   // ps 这里是你要引用的项目的地址配置
}

exposes: {
    // key: value
    './button': 'src/pc/components/button',
}

shared: 我们在使用远程组件的时候，这个组件如果依赖了某个第三方包，那么它就会优先从当前应用获取, 如果没有的话则从远程组件进行拉取

关于 remotes && exposes, 不难看出

remotes: 远程引用

exposes: 远程暴露

```

> 好了 大致的参数以及配置使用我们了解了下, 其实不难发现 remotes 通过引用 xx.js 拿到该文件然后直接拿到 远端设定的组件 从而达到渲染的效果！

ps. 对于配置是比较简单的但是 如果往成熟的应用中切入其实是要有一些问题要去仔细考量的, mf本身就不是插拔式直接嵌入到你的应用, 当然了如果一个全新的应用去从零开始配置webpack 成本也比较大, 基于webpack封装之后的框架当然了也要好好去研究如何配置, 本文以官方文档给出的ts版本的react 介绍!

## ts react demo

> **我们大致可能猜测是个什么样子的 有点像在线引入了一个js sdk 然后host项目可以直接使用remote组件内部的组件**

```js

baseComponent (remote)
---index.js 入口文件
---bootstrap.js 启动文件
---App.js react组件
---button.jsx 用于暴露的组件 ...


project (host)
---index.js 入口文件
---bootstrap.js 启动文件
---App.js react组件


> 入口文件一定要有否则跑不起来!

```

![参考如图](https://hulaplanet.oss-cn-shenzhen.aliyuncs.com/static/asset/dd.png)

左图代表remote 被引用组件的应用
右图代表host 引用组件的应用

> 通过上图的加载顺序: 我们可以发现js加载顺序按照

- 先加载main.js 
- 拉取远程remoteEntry.js
- 通过share 拉取共享npm 包
- 通过expose 拉取远程需要的chunk包

文件分为3类: 1. main.js  2. remoteEntry.js  3. 异步加载的文件()，main.js/remoteEntry.js 里加载的文件

- 未完待续

参考文章

1. [webpack 中文文档](https://webpack.docschina.org/concepts/module-federation/)
2. [Webpack 5 Module Federation: JavaScript 架构的变革者](https://zhuanlan.zhihu.com/p/120462530)
3. [module Federation原理学习](https://github.com/efoxTeam/emp/wiki/%E3%80%8Amodule-Federation%E5%8E%9F%E7%90%86%E5%AD%A6%E4%B9%A0%E3%80%8B)
4. [官方demo](https://github.com/module-federation/module-federation-examples)