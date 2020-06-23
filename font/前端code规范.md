## 前端代码规范

---

#### 1. 项目文件目录规范

- 关于目录文件命名: AbbCdd / aa_bb (一般是这2种), 项目中命名就统一用其中的一种, 不要混着用

- 统一项目文件名参考如下

```
- assets 静态资源
- pages  页面组件
- components 公共组件
- global 公用配置文件夹
- native 原生模块(android/ios)
- server 请求数据
- stores 状态机
...
```

- 关于项目命名: aa-bb-cc 

---

#### 2. 命名

> ***2.1 js变量声明***

- booean值类型的申明: isXxx / hasXxx

```js
// good:
const isContentPage = true
const hasContains = false

// bad:
const currentBoolean = true
```

- 常量声明

- 变量声明

> ***2.2 函数声明***

> ***2.3 组件声明***

> ***2.4 css 命名***

---

#### 3. eslint 规则

- 具体规则去参照: eslint-config-5e 规则集合, 目前包括: base/react 2大类
[gitlab地址](https://git.dev.moumoux.com/)

```
主要是针对 js/ts/tsx 语法进行了部分限制,针对这些规则,会不断完善 后者大家觉得需要优化的规则可以提出来进行讨论修改
```

---

#### 4. typescript

- 申明类型: any 一般情况下我们写的业务代码是需要具体到类型的，当然如果是特别通用的申明是可以用泛型


#### 5. 单元测试

---

#### 工作细节

1. 如果code 的pr 是解决了哪些issue, 在comment中需要带上 issue编号

2. 项目中特殊的操作可以记录留痕: Readme.md, FIX. TODO 记录一下

```
- 对于Readme.md 首先我们要重视, 比如你接入了第三方的东西 就可以记录留痕
- 编码过程如果遇到比较难处理的问题,然后找到方式去解决都可以做个记录
- 非常规的代码为什么要这么写,搞点注释加上去可以,有助于大家理解
```

3. 对于react中组件中的合成方法:

```js
// good ?
<A onFocus={() => setFlag(flase)} onBlur={() => setFlag(true)} />

// bad  ?
<A onFocus={changeFlagStatus(false)} onBlur={changeFlagStatus(true)} />

对于这个默认在 合成事件中的方法: 优先在 组件内部去书写方法.

1. 首先你的函数不确定只有这一个逻辑, 提到外面是有助于拓展的(如果有额外的逻辑)
2. return 是写dom的地方,没必要写额外的js吧
```

4. 关于useEffect 如果你进行封装的话,那么依赖的参数就用deps

```js

// good
const useXX<T> = (fn: () => void, deps= T[] ) => {
    useEffect(() => {
        ...
        fn()
        ...
    }, [deps])
}

// bad
const useXX<T> = (fn: () => void, params= T[] ) => {
    useEffect(() => {
        ...
        fn()
        ...
    }, [params])
}
```

5. 关于hook: useState 比较简单可以省略掉声明

```js
// bad
const [value, setValue] = useState<string>('')

// good
const [value, setValue] = useState('')
```

6. 关于命名:

```
需要严格按照上面的介绍情况处理
```

#### git工作流

---

#### 新人任务

- 要把所有的系统和流程跑一边,通过实际项目和系统的直观体现能够更加的了解在做什么产品
- 针对系统和项目,可以在体验过程提出自己的建议和想法, 比如app 哪些模块用的很卡顿, 又或者dashbord 项目设计有些操作不够人性化

---

#### 关于前端项目简介

参考文章:
1. [腾讯团队编码规范](http://alloyteam.github.io/CodeGuide/)