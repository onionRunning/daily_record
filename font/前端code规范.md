# 前端代码规范记录篇

---

## 1. 项目文件目录规范

- 关于目录文件命名: AbbCdd / aa_bb (一般是这2种), 项目中命名就统一用其中的一种, 不要混着用

- 统一项目文件名参考如下

```md
- assets 静态资源
- pages  页面组件
- components 公共组件 : base(基础组件)/bussiness(业务组件)
- global 公用配置文件夹 : config, utils, ...
- native 原生模块(android/ios)
- server 请求数据
- stores 状态机
...

```

> 好的目录结构划分可以让你更加理清楚

- 关于项目命名: aa-bb-cc

---

## 2. 命名

> ***2.1 js变量声明***

- boolean值类型的申明: isXxx / hasXxx / canXxx

```js
// good:
const isContentPage = true
const hasContains = false

// bad:
const currentBoolean = true
```

- 常量声明

> 针对与常量的申明: 就用 UPPER_CASE 这种形式: AAA_BBB / AAA

```ts
// good
const NAV = ['test1', 'test2']

// bad
const Nav = 'update/getCurrentPages'

// good
enum NAV_TITLE {
  all = '文章列表',
  draft = '草稿',
}

// bad
enum NavTitle {
  all = '文章列表',
  draft = '草稿',
}

```

- ***变量声明***

对于变量首先是Camel驼峰命名, 尽可能用英文去描述性命名,尽量不要搞些拼音 😄

```js
// bad
const xiazai_quan = 'xxxx'

// good
const downloadCircleContent = {}
```

> **如果要用到下列变量按照这个格式就行: ID URL Android IOS**
> ***2.2 函数声明***

动作 + 描述词 eg: 改变输入框值: changeValue
常见动词: change, click, handle , get , set, load ...

> ps. 有些常见名词尽量不要省略，eg: changeIptVal(不是很友好哈)

```js
// good
const changeInputValue = () => {}

// bad 这样描述就给人感觉不清晰
const iptValue = () => {}

// bad
const changeIptVal = () => {}

// good
// 如果是组件内部对外暴露的方法
// 如果是类似的逻辑 可以优先用合成事件的名称
<CirCleItem onClick={} onChange={} />

```

> ***2.3 组件声明***

帕斯卡 命名,尽可能带有描述性

```js
// good
const IdiomListItem = () => { return </> }

// bad
const Item = () => { return </> }

```

> ***2.4 css 命名***

```css
/* 稍微语义化点 */

/* // good */
.nav_lists {

}

/* // good */
.nav-lists {

}

对于属性的排列: 可以按照规范来

```

---

### 3. eslint 规则

- 具体规则去参照: eslint-config-5e 规则集合, 目前包括: base/react 2大类
***[gitlab地址](git@git.dev.moumoux.com:base/eslint-config-5e.git)***

> 主要是针对 ts/tsx 语法进行了一些约束,针对这些规则,会不断完善 或者大家觉得需要优化的规则可以提出来进行讨论修改

- rn 项目中如果遇到有些格式上的冲突，可以先从代码层面进行部分调整,然后在考虑规则的问题

---

#### 4. typescript

- 申明类型: any 减少使用 一般情况下我们写的业务代码是需要具体到类型的，当然如果是特别通用的申明是可以用泛型
- 写不合规范类型后,组件引用后，vscode 会有提示报错,需要去解决这个报错哈,虽然程序运行不会报错，但是说明你写的类型不对啊

#### 5. 单元测试

```md
不需要都写，但是通用的方法需要去写,当然是有目的的去写: 测试哪些有输入输出的
```

---

#### 工作细节

> 这个是我这几个月记录的一些 merge过程中,被提出的一些问题

- 1 如果code 的pr 是解决了哪些issue, 在comment中需要带上: close issue编号, 合并后就自动关闭这个请求

- 2 项目中特殊的操作可以记录留痕: Readme.md, FIX. TODO 记录一下

```md
- 对于README.md 首先我们要重视, 比如你接入了第三方的东西 就可以记录留痕
- 编码过程如果遇到比较难处理的问题,然后找到方式去解决都可以做个记录
- 非常规的代码为什么要这么写,搞点注释加上去可以,有助于大家理解
```

- 3 对于react中组件中的合成方法:

```js
// A
<A onFocus={() => setFlag(flase)} onBlur={() => setFlag(true)} />

// B
<A onFocus={changeFlagStatus(false)} onBlur={changeFlagStatus(true)} />

- 如果只是单行逻辑的话，可用自定义hook 或 上述的A种方式
- 多行逻辑的话建议把函数丢除去

return 是写dom的地方,如果写过多的js逻辑,感觉有些干扰

```

- 4 关于useEffect 如果你进行封装的话,那么依赖的参数名用deps

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

- 5 关于hook: useState 比较简单可以省略掉声明

```js
// bad
const [value, setValue] = useState<string>('')

// good
const [value, setValue] = useState('')

```

- 6 关于命名:

```md
需要严格按照上面的介绍情况处理
```

- 7 styleComponents

```js
- 申明首字母要是s
```

---

### 可以非必要强制遵守

#### 1. 使用可搜索的名称

我们要阅读的代码比要写的代码多得多， 所以我们写出的代码的可读性和可搜索性是很重要的。 使用没有
意义的变量名将会导致我们的程序难于理解， 将会伤害我们的读者， 所以请使用可搜索的变量名。

bad

```javascript
// 艹， 86400000 是什么鬼？
setTimeout(blastOff, 86400000)

```

good

```javascript
// 将它们声明为全局常量 `const` 。
const MILLISECONDS_IN_A_DAY = 86400000

setTimeout(blastOff, MILLISECONDS_IN_A_DAY)

```

##### 2. 使用解释性的变量

bad

```javascript
const address = 'One Infinite Loop, Cupertino 95014'
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2])
```

good

```javascript
const address = 'One Infinite Loop, Cupertino 95014'
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
const [, city, zipCode] = address.match(cityZipCodeRegex) || []
saveCityZipCode(city, zipCode)
```

#### 3. 函数应当只做一件事情

这是软件工程中最重要的一条规则， 当函数需要做更多的事情时， 它们将会更难进行编写、 测试和推理。
当你能将一个函数隔离到只有一个动作， 他们将能够被容易的进行重构并且你的代码将会更容易阅读。 如
果你严格遵守本指南中的这一条， 你将会领先于许多开发者。

#### 4. 移除冗余代码

竭尽你的全力去避免冗余代码。 冗余代码是不好的， 因为它意味着当你需要修改一些逻辑时会有多个地方
需要修改。

你有冗余代码通常是因为你有两个或多个稍微不同的东西， 它们共享大部分， 但是它们的不同之处迫使你使
用两个或更多独立的函数来处理大部分相同的东西。 移除冗余代码意味着创建一个可以处理这些不同之处的
抽象的函数/模块/类。

让这个抽象正确是关键的， 这是为什么要你遵循 *Classes* 那一章的 SOLID 的原因。 不好的抽象比冗
余代码更差， 所以要谨慎行事。 既然已经这么说了， 如果你能够做出一个好的抽象， 才去做。 不要重复
你自己， 否则你会发现当你要修改一个东西时时刻需要修改多个地方。

---

参考文章:

1. [腾讯团队编码规范](http://alloyteam.github.io/CodeGuide/)
