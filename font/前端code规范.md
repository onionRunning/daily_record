## 前端代码规范

---

#### 1. 项目文件目录规范

- 关于目录文件命名: AbbCdd / aa_bb (一般是这2种), 项目中命名就统一用其中的一种, 不要混着用

- 统一项目文件名,

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

---

#### 2. 命名

> ***2.1 js变量申明***

- booean值类型的申明: isXxx / hasXxx

```js
// good:
const isContentPage = true
const hasContains = false

// bad:
const currentBoolean = true
```

---

#### 3. eslint 规则

- 具体规则去参照: eslint-config-5e 规则集合, 目前包括: base/react 2大类

```
主要是针对 js/ts/tsx 语法进行了部分限制,针对这些规则,会不断完善 后者大家觉得需要优化的规则可以提出来进行修改
```

---

#### 4. typescript

- 申明类型: any 一般情况下我们写的业务代码是需要具体到类型的，当然如果是特别通用的申明是可以用泛型



---

#### 工作细节

1. 如果code 的merge 是解决了哪些issue, 在comment中需要带上 issue编号

---

参考文章:
1. [腾讯团队编码规范](http://alloyteam.github.io/CodeGuide/)