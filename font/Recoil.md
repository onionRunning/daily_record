# react Recoil

顶层组件: 基本上主流的状态机需要写 个顶层root 然后再把值给传进去， 这里可以不用手动传value

```ts
<RecoilRoot>
  <Root />
</RecoilRoot>
```

## 基础api

### 1.  atom 原子函数
 
```ts
atom<T>({
  key: 'string', //  命名空间
  default: T | Promise<T> | RecoilValue<T> // 初始值 
})
// eg: 
const loginState = atom({
    key: 'loginState',
    default: {status: 'init', token: ''}
}) 
const Login = () => {
  const [state, setState] = useRecoilState(loginState) // 读写
  const reset = useResetRecoilState(loginState)        // 重置 
  const onlySet = useSetRecoilState(loginState)        // 写
  const onlyget = useRecoilValue(loginState)           // 读

  ....
}

```

### 2. selector 选择器函数， atom 的补充, 可以组合多个 atom 得到你的数据

```ts
selector<T>({
  key: 'string', // 
  get: ({
    get: GetRecoilValue
  }) => T | Promise<T> | RecoilValue<T>,  // 计算得到值的
  set: ({get: GetRecoilValue, set: SetRecoilState, reset: ResetRecoilState}, newValue) => void
})

const commonStore = selector({
  key: 'common_store',
  get: ({get}) => get(loginState),
  set: ({set}, value) => set(loginState, value as number) 
})

const Xx = () => {
  const loginValue = useRecoilValue(loginState)
  const common = useRecoilValue(commonStore)
  const commonOperate = useSetRecoilState(commonStore)
  return <div onClick={() => {commonOperate(10000)}}>当前的值是: {loginValue},----- {common}</div>
}

tips: 
// 1. atom 发生更新后, selector get函数中有依赖项, 也会同步更新
// 2. selector set 可以更新  atom 的 value  
// 可以发现无论是 atom 还是 selector 关注点都在 default 比较纯粹的做数据处理
```

### 3 base hooks

```ts
1. useRecoilState 具有对 atom 或 selector get 或 set 操作
function useRecoilState<T>(state: RecoilState<T>): [T, SetterOrUpdater<T>]
type SetterOrUpdater<T> = (T | (T => T)) => void

const [getValue, setValue] = useRecoilState(loginState)


2. useRecoilValue 获取 atom 或 selector 的值
function useRecoilValue<T>(state: RecoilValue<T>): T  

const getValue = useRecoilValue(loginState)


3. useSetRecoilState 设置 atom 或 selector 的值

function useSetRecoilState<T>(state: RecoilState<T>): SetterOrUpdater<T>;
type SetterOrUpdater<T> = (T | (T => T)) => void;

const setValue = useSetRecoilState(loginState)


4. useResetRecoilState 重置  atom 或 selector 的值
function useResetRecoilState<T>(state: RecoilState<T>): () => void

const resetLogin = useResetRecoilState(loginState)

ps. 居然提供了重置为初始值的hook, 真的不错


5. useRecoilValueLoadable 获取异步选择器的状态信息
function useRecoilValueLoadable<T>(state: RecoilValue<T>): Loadable<T>

const getPromise = async () => {
    return await new Promise(res => {
        setTimeout(() => {
            res('6666')
        },10000)
    })
}
const basePromise = atom({
    key: 'promise',
    default: getPromise()
})

...
const baseContent = useRecoilValueLoadable(basePromise)
...

baseContent:  Loadable 
state: hasValue /  hasError / loading      3 种状态
contents: actual value / Error / Promise   状态对应的详情

6. 判断是否是 recoil 类型的值
isRecoilValue(loginState) -> true
isRecoilValue(666) -> false

```

### 4. snapshot 快照 大致看一下知道有这个东西就行

```ts

class Snapshot api 如下

getLoadable: <T>(RecoilValue<T>) => Loadable<T>
getPromise: <T>(RecoilValue<T>) => Promise<T>

map: (MutableSnapshot => void) => Snapshot
asyncMap: (MutableSnapshot => Promise<void>) => Promise<Snapshot>

map: (MutableSnapshot => void) => Snapshot
asyncMap: (MutableSnapshot => Promise<void>) => Promise<Snapshot>



1. useRecoilCallback

const loggg = useRecoilCallback(({snapshot}) => async() => {
  const getBase = await snapshot.getPromise(basePromise)
  const getBase3 =  snapshot.getLoadable(basePromise)
  console.log(getBase, 'getBase', getBase3, getBase2)
})

...
<div onClick={loggg}>点我点我</div>
...
 
```

**其实到这里项目中如何去使用你的recoil 差不多有个清晰的思路了, 我们去定义 基础atom， 或组合的selector ， 异步的数据你可以通过调用请求然后去set, 或者依靠 atom/selector 异步default 去设置你的数据模型, 并且我们可以发现atom 这个东西的侧重点在default(也就是数据共享) ， 模型层只提供 zet 或 get 还有 reset, 但是selector 拓展性就更强， 有set 可以去设计额外的逻辑 ***

> 还没完 recoil 还提供了一些更加拓展的api如下:

### 5. utils

> **atomFamily and selectorFamily**

```ts

function atomFamily<T, Parameter>({
  key: string,
  default:
    | RecoilValue<T>
    | Promise<T>
    | T
    | (Parameter => T | RecoilValue<T> | Promise<T>),
  dangerouslyAllowMutability?: boolean,
}): Parameter => RecoilState<T>


const familys = atomFamily({
  key: 'familys',
  default: (p) => new Promise(res => {
      setTimeout(() => {
          res(p)
      },1000)
  }),
})
const Family = ({elementID = 10}: any) => {
  const position = useRecoilValueLoadable(familys('1'));
  return (
    <div>
      family: {console.log(position.contents, 'useRecoilValueLoadable')}
    </div>
  )
}
// selectorFamily
function selectorFamily<T, Parameter>({
  key: string,
  get: Parameter => ({get: GetRecoilValue}) => Promise<T> | RecoilValue<T> | T,
  dangerouslyAllowMutability?: boolean,
}): Parameter => RecoilValueReadOnly<T>

const myNumberState = atom({
  key: 'MyNumber',
  default: 2,
})
  
const myMultipliedState = selectorFamily({
  key: 'MyMultipliedNumber',
  get: (multiplier: number) => ({get}) => {
    return multiplier * get(myNumberState)
  },
  set: (s) => ({set}) => {
    set(myNumberState, s)
  }
})
const Family2 = () => {
  const number = useRecoilValue(myNumberState);
  const addNums = useRecoilValue(myMultipliedState(100));
  const setNums = useSetRecoilState(myMultipliedState(100))
  return <div onClick={() => {
    setNums(1000)
  }}>...number: {number} <br/> multipliedNumber: {addNums}</div>;
}

ps. atomFamily 还是 selectorFamily 相比 atom/selector 而言 可以初始化传参数
```

> **waitForAll waitForAny waitForNone**

```ts

waitForAll: 等多个异步atom 都有返回值时， 状态才更新

waitForAny: 等任意一个异步atom 都有返回值时， 状态才更新

waitForNone: 直接返回成功的状态

- 这3 个api, 类似promise 里的api

ps. 异步获取数据时， 只能用 useRecoilValueLoadable 这个hook 去获取state/content

```

## summary

1. atom 适合用于基础数据, selector 适合数据组合, 或拓展额外操作逻辑 在 set里面
2. 关于hook 或获取同步或异步的, 看自己需求选就行, 异步逻辑可写在基础函数 也可以 利用set 储存数据
3. 关于 hook自己看情况使用, 同步或异步状态下使用的hook不一样
4. 是否支持rn？ 直接装是跑不起来的, 是有一些补丁的方式可以去集成
5. 作为一款官方推出的状态管理确实搭配hook 使用起来还是比较舒服的,

## 参考文章

1. [recoil官网](https://recoiljs.org/docs/api-reference/core/isRecoilValue)