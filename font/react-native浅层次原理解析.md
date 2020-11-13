### react native

> 背景: rn,跨平台框架 - write once , run anywhere 一定程度上 技术壁垒 + 成本

跨平台: 

```
混合app

1. H5 + codava(phoneGap) + Webview(android)/ UIWebview   eg: ionic

原理: 原生组件webview 显示 H5, js 通过phonegap 调取原生api(拍照...)
缺陷: 兼容性 + 体验


2. React Native

原理: js 通过 C++ bridge 在个平台上(android, ios , web)映射为原生组件渲染, 
缺点: 无法保证组件的平台一致性, 需要针对不同功能去编写 android/ios 的桥接功能, 体验稍微与原生有小差距 

3. Flutter

原理: 渲染引擎依靠跨平台的Skia图形库来实现，依赖操作系统图形绘制相关的接口，可以在最大程度上保证不同平台、不同设备的体验一致性
缺点: 生态 + 难度(dart)

```     

---

#### 核心概念

> react 与 react native是啥关系?
> 为什么能够跨平台?
> 一些核心的概念你需要理解?


***为一个Rn 开发者基本上上面3个问题你需要有自己的理解和思考***

---

#### Rn 技术架构 

看看偷过来的Rn 技术架构图![Rn技术架构图](https://poetries1.gitee.io/img-repo/2019/10/680.jpeg)

这个图还是比较清晰的对 Rn进行描述

- 绿色: 我们编写的React组件,js 逻辑等等
- 蓝色: 工具库..., React.js,Fetch, npm, node, redux
- 黄色: 我们写的一些原生方法或组件,通过桥的方式可以在js ui层直接调用
- 红色: android/ios 平台自己的原生api， 黄色的一些操作就是调用红色的api

- 我们一般写绿色比较多, 当然蓝色我们也可以搞一些基建的东西搞成npm包的形式, eslint-config-5e 就是一个简单的demo, 黄色也有东西, 比如瀑布流组件, 原生通知等等

---

#### 组成

javascriptCore + reactJs + bridges -> react native

javascriptCore 解析js
reactJs ui 绘制
bridges 翻译原生组件进行绘制， 把原生组件的用户事件反馈给reactJs

---

##### js vdom -> 原生组件(android/ios)

[图片来源](https://zhuanlan.zhihu.com/p/32749940)

![偷偷的第二张图](https://pic1.zhimg.com/80/v2-990aa3a1c34a8e1b956baaa00b4ca9db_1440w.jpg)
![第三张图](https://upload-images.jianshu.io/upload_images/1055199-a7fe1d821fb361b7.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

js vdom -> 如何转换成 android/ios 控件, 有个Bridge(桥)
Bridge: 用来翻译js的绘制指令给原生组件进行绘制，同时把原生组件接收到的用户事件反馈给js


vdom -> 原生控件

原生事件 -> js 事件 

js逻辑 -> 调用原生事件

```
// 还记得上面的 rn架构里的yoga (c 写的), 是一个跨平台的布局引擎
1. vdom -> YogaTree
把我们的组件里的，flex 布局的关键属性 以及 css布局样式转换成YogaNode 生成一个 YogaNodeTree 

2. yogaNode -> native element 然后就渲染成原生组件


vNode -> YogaNode 是通过 MessageQueue（bridge在 js层面的代理） 指令队列, 深度便利vdom节点然后发给 yoga 转换成 yogaTree   bridge通过通知 原生平台去绘制相应的组件

```

##### 初始化

- 原生代码初始化
- js引擎初始化， js运行环境并且加载js bundle
- 开始跑js 代码， ui开始绘制
...

##### 通信



---

#### 开发

> 如果你会react开发, 其实你就已经掌握了差不多50%的开发能力, 剩下的30% 是RN自带的一些api 或属性,  剩下10%左右是 原生代码 这个有能力可以进行了解

##### 1. web区别

> 在编写React Native 组件时, web 端的标签 需要全部换成 react-native 这个库提供的base 组件

```
div  ->  View
span ->  Text
img  -> Image

ps. 编写阶段不能像web端随意,比如 字符串必须被 Text wrapper ...
```

---

##### 2. 样式

> 关于样式, web端我们习惯写class, className , react native 统一写 style={styles.xxx} 就是类似 cssModule的写法 不同的是 styles 是个对象

```
import {StyleSheet} from 'react-native'

const styles = StyleSheet.create({
  wrapper: {
    flex: 1,
  },  
})
```

##### 3. 样式书写细节       

> 关于样式的属性, 其实与web端差不多对应,但是是小驼峰写法 并且天然就是 flex布局(你可以理解每个标签默认添加: display: flex)

```
font-size -> fontSize
padding-bottom -> paddingBottom

ps. 当然还有 position: relative, 这个定位是相对于你的兄弟元素而言 等细节.

```

problem: ui 编写的时候尽量不要用定宽高的方式?

> 不同的机型尺寸可能不太一致， eg: width: 375 - 16 * 2, 可用 flex: 1, padding 左右各16 替代, 当然也可以重新计算尺寸 用 375 这个尺寸为base


---



##### 4. 常用的基础组件

```
baseComponent:

View
Text            
Image 
FlatList
ScrollView
TouchOpacity
...

---

事件: 不同的base组件有各自对应的事件, 看情况去用就行
onPress
onScroll
...

---

api:

Animate
手势
...

```

---

##### 5. 路由

> 关于路由这块目前使用的react-navigation 这个官方推荐库

我们常用的是: createStackNavigator, createBottomTabNavigator

stack + tab 目前的路由写法

```js
const Tab = createBottomTabNavigator()
const Stack = createStackNavigator()

// component - 组件
// name - 路由名 跳转专用 ...
// tab:
<Tab.Navigator>
  <Tab.Screen component={B} name='B'>
  <Tab.Screen component={A} name='A'>
</Tab.Navigator>

// stack
<Stack.Navigator>
  <Stack.Screen component={C} name='C'>
  <Stack.Screen component={D} name='D'>
<Stack.Navigator>

这个写法是不是感觉和web端自定义很熟悉, 当然上面的组件还有一大堆的 api去配置(设置头) ...

路由跳转的话: A,B,C,D 这样直接被路由组件wrapper的 组件可用, props.navigation.push('B') 

props.navigation 又是一个大对象,有很多api: push/replace/navigate ... 可以自行查看对应的哈

当然也有提供路由hook，   const navigation = useNavigation() api 较上面的方式少一些

还有路由带参数...

```

> 当然其实是有一些瓶颈

1. 路由切换动画时会有少许掉帧
2. 有些页面我想用原生组件,跳转逻辑比较复杂...

于是现在就在搞原生路由 -> 去解决上面2种场景


---

#### 5.1 react-native-5e-navigation 路由

[react-native-5e-navigation](http://dev.moumoux.com:9088/-/web/detail/react-native-navigation-5e)

#### 6. network

这个和 web端差不多就是 封装的 axios

---

#### 调试

- 常见打 log, 你写的逻辑大都是js
- 虚拟机自带工具
- 常用三方工具...

#### 打包

- android 
- ios

---


#### 参考文章

1. [React Native 之原理解析](https://zhuanlan.zhihu.com/p/86836826?utm_source=wechat_session)
2. [React-Natie的实现原理](https://www.jianshu.com/p/8fc5015f4180)
3. [Flutter 原理](https://tech.meituan.com/2018/08/09/waimai-flutter-practice.html)
4. [react-navigation](https://reactnavigation.org/docs/getting-started)
5. [Chrome V8引擎介绍](https://blog.csdn.net/heyeqingquan/article/details/78839699)
6. [React native渲染原理](https://www.jianshu.com/p/7e7b462c187b)
7. [ReactNative 知识小集(2)-渲染原理](https://zhuanlan.zhihu.com/p/32749940)
8. [深入理解react-native](http://blog.ilibrary.me/2016/12/25/react-native-internal)