# webpack

## 背景

```md
1. 一个js程序的静态模块打包工具, 处理时会在内部从一个或多个入口点构建一个依赖图!将项目中所需要的模块组合成一个或多个bundles(静态资源)!

2. 需要理解的包含:

- 入口 (entry)

- 输出 (outputs)

- loader (加载器)

- 插件(plugin)  (插件)

- 模式          (mode) 

- 浏览器兼容性

- 环境
```

## 配置

### 入口

> webpack应该使用那个模块作为内部构件图的开始!

```md

module.exports = {
    entry: path.join(__dirname, './path/xx/xx/index.tsx')
}
```

参考文章

1. [webpack优化](https://juejin.cn/post/6844904093463347208#heading-17)
2. [webpack中文文档](https://webpack.docschina.org/concepts/)
