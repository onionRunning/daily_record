1. react Recoil 

顶层组件:

```
<RecoilRoot>

</RecoilRoot>
```

2.  atom 原子函数
 
```ts
atom<T>({
  key: 'string', //  整个程序独一无二的
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
})
```