# 亲子RN 架构

- 待续

---

## 编码规范

- 前提参考: 前端相关的一些规范 [前端code规范](https://github.com/onionRunning/daily_record/blob/master/font/%E5%89%8D%E7%AB%AFcode%E8%A7%84%E8%8C%83.md)

---

### 1. check 组件的render次数, 避免非必要render, debugger-ui or 工具 一定要重视

```md
app端 是比较明显的,如果多次触发render 会造成 ui掉帧, 需要重点注意⚠️
```

---

### 2. 一个文件, 只写一个组件， 避免额外组件进行干扰

```md
文件尽量有主次, 一个文件下 有 n个组件 其实一眼看下去不直观, 不是很友好
```

---

### 3. 函数不要超过50行, 过长进行拆分

```md
其实当你的函数超过30行的时候 其实就已经很长了, 不说单一原则吧, 尽可能减少函数代码行数, 是方便维护与理解
```

---

### 4. 与组件内部无关函数尽量提到外面,解耦组件内部函数相关逻辑

```md
有些函数或配置完全是与组件没什么关系的, 这种可以提取到 utils/ const 下， 或者提到组件外面, 然后这种函数测试的意义就比组件内部测试大
```

---

### 5. 组件的 style相关逻辑可以新建 index.style.ts 专门放置样式 (参考web端, css/js 分离)

```md
个人是喜欢这样抽离处理, 当然如果组件不叫简单可以忽略, 如果涉及到样式与你的props/state 相关， 可以用函数的方式去编排, 相信你可以懂得， 在我看来 .tsx文件我更加倾向放 组件逻辑 或申明，或js逻辑， 常量，公共函数 放到 utils.ts 
```

---

### 6. 代码风格统一, react 都采用hook的方式书写, 老代码可以暂时忽略

### 7. 尽可能减少render 节点层级, 节点的层级过深, 是对ui会有些许影响的

```md
我们在写ui的时候 一般都想好如何去编写你的层级,可以减少标签的个数
```

---

### 8. 后端返回的数据，需要进行兼容性判断(放置崩溃)

```md
eg: 后端返回, res: {data: {medias: [{url: 'xxx'}]}}

我们用的时候就直接: res.data.medias[0].url, 这样非常容易造成崩溃, 不要太相信后端返回的数据类型以及xxx
需要这样处理: res?.data?.medias[0]?.url

```

---

### 9. 关于组件属性设计, 减少组件对外暴露的属性

```js
一个组件应该是有特定的暴露属性, 不要添加太多,如果一下子看到一大堆组件的属性,给人不直观的体现, 并且不要每次新增新的需求就 加一个属性

// bad
<AXXssAAA
    click={xx}
    clickOpen={yy}
    xx={xy}
    isxx={xxx}
    isyy={yyy}
    data={xo}
    list={xu}
    config={kl}
    trans={ll}
>

// good

<AaaBbb
  list={data}
  model={xx}
  clickOpen={() => {}}  
>
ps. 组件对外交互的属性应该是我们思考好的， 尽量简洁易懂

```

---

### 10. 避免冗余代码

```md
我们在编码过程会有些代码 用eslint 规则检测不出来的, 这个需要自己去评定。
1 有哪些变量或函数是可以不需要额外定义,
2 有哪些函数变量是可以整合或抽象,
```

---

### 11. 关于帧率可以参考的文章, 如何优化卡顿的解决思路

```md
https://blog.csdn.net/ll1109489444/article/details/64437883?utm_source=blogxgwz5
```

- 待续

---

## 参考文章

[react-native 性能优化，处理卡顿](https://blog.csdn.net/ll1109489444/article/details/64437883?utm_source=blogxgwz5)