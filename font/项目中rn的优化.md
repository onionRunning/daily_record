### 本文讲解关于rn 的一些优化或使用经验

#### 调试工具

> 用过哪些工具

##### 1. debugger-ui

```
debugger-ui 直接打印显示比较简单
```

##### 2. react-devtools

```
1. 安装: npm install -g react-devtools (安装过程比较慢)
2. 启动: react-devtools
3. 如果没有connect : adb reverse tcp:8097 tcp:8097
[github 地址](https://github.com/facebook/react-devtools/tree/master/packages/react-devtools)
4. 一般用做样式调整
```

##### 3. Reactotron

```
下载Reactotron客户端地址: 
项目中集成Reactotron
yarn add reactotron-react-native -D
reactnative项目中引用: 

import Reactotron,{overlay, openInEditor, networking} from 'reactotron-react-native'
import { mst } from "reactotron-mst"
import store from "./store/root"

const Reacto = Reactotron
  .configure({
    name: "start ReactNative"
  })
  .use(mst())          // 打印 mobx-state-tree 的action... (这里用的状态机mst)
  .use(networking())   // 打印网络请求 
  .connect()           //  开始connect
  .trackMstNode(store,'store')   
export default Reacto
gobal._logger = Reacto  //  全局挂载

ps. 如果你遇到未connect How to connect to Reactotron from Android Phone?
执行下列操作: adb reverse tcp:9090 tcp:9090
```

---

#### rn 项目规范

> 好的目录结构以及清晰的模块划分会让人更加的理清业务逻辑

- 合理规范你的项目目录

```
- Asserts
- Components
- Pages
- Store
- Server            
- Global
- Native    // 原生调用模块
...

1. 目录按照规范设定
2. 我们写的比较多的是 Pages 下的逻辑,我们定义的时候按照模块区划分我们的组件,同理状态机也可以采用同样的处理方式
```

---

#### rn之前的优化方案(大体方向,具体方案细节需要考虑)

> 针对rn app我们之前主要是针对android 平台去比较简单层面的优化

##### 1. 接口优化,上下行流量优化

> 减少非必要接口的重复请求(带宽,与用户体验),对于相应字段可以有需要返回

##### 2. 启动页优化

> 点击logo图标,首页启动的时长需要减少: 资源预加载

##### 3. 体积优化

> 减少app包的体积: 资源大小优化

##### 4. 针对无网，弱网 给用户友好提示(弹窗提示 or 展示相应的状态)

##### 5. 本地缓存

> 内置本地sql,对一些特殊接口内容进行缓存处理: 减少请求提升效率, 无网弱网状态下(还是有些内容可见)

##### 6. 流畅度

> 体验方面是否出现掉帧卡顿,这个需要通过工具查看帧率的范围,确定是代码层面还是哪里出现的问题

##### 7. 前端单元测试 and 自动化测试 (不用多说)

#### 针对hula-qinzi项目中的体验问题
- 首页/呼啦圈 快速点击，路由会跳转2次 需要添加防抖
- 有些模块出现ui 掉帧的情况, 特别明显的就是 一些模块路由切换时触发出现卡顿的情况