### 状态机

> 我们为什么需要用状态机?

- 数据共享
- 代码分层

### react Recoil 

顶层组件:

```ts
<RecoilRoot>
  <Root />
</RecoilRoot>
```

2.  atom 原子函数
 
```ts
atom<T>({
  key: 'string', //  命名空间
  default: T | Promise<T> | RecoilValue<T> // 初始值 
})
```

3. selector 选择器函数

```ts
selector({
  key: 'string', // 
  get: ({
    get: GetRecoilValue
  }) => T | Promise<T> | RecoilValue<T>,  // 计算得到值的
  set: () => {}
})
```