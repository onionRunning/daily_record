## react-hook

### 常用api hook 以及通常用法

#### useState

> 我们用它来更新组件, 自定义hook数据

```js
function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>];

tips: 
1. initialState: 可以为默认值, 也可以返回默认值的函数, useState('test') or useState(() => 'test')
2. 如果内部需要的状态比较多，可以直接设置对象集合代替单个属性

// way1
const [isLoading, setLoading] = useState(false)
const [isShowModal, setShowModal] = useState(false)
const [currentPage, setCurrentPage] = useState(1)
const [list, setList] = useState([])
...

// way2
const [goodStatusParams, setGoodStatusParams] = useState({
    isLoading: false,
    isShowModal: false,
    currentPage: 1,
    list: []
})
// 官方介绍: 我们推荐把 state 切分成多个 state 变量，每个变量包含的不同值会在同时发生变化。
// 俩种方式我觉得都可以,


const [num, setNum] = (0)

// key1
const onclick = () => {
    setNum(1)
    setNum(2)
    setNum(num + 1)
    setNum((t) => t + 1)
}

// key2
const onclick1 = () => {
    setNum((t) => t + 1)
    setNum((t) => t + 2)
}

// key3
const onclick1 = () => {
    setNum((t) => t + 1)
    setNum((t) => t + 2)
    setNum(num + 1)
    setNum((t) => t + 2)
}

// output
console(num)

> 多个setXx 执行后,render的打印情况（当然我们编码时不会写这样的代码，这样只是说明你是不是比较了解这个底层机制）

// 记录一下, 后续源码分析的时候用上.
```

#### useEffect

> class组件中生命周期职责类似

```js
function useEffect(effect: EffectCallback, deps?: DependencyList): void

type EffectCallback = () => (void | (() => void | undefined));

type DependencyList = ReadonlyArray<any>; 只读数组类型

// @ReadOnly deps 发生改变时 -> effect回执行


// eg: DidMount
useEffect(() => {
  const getHttpRes = async () => {
    const res = await http.get('/xxx')
    setCommentData(res.data || [])
  }  
  getHttpRes()
}, [])

// addEventListener
useEffect(() => {
  window.addEventListener('resize', changeResize)
  return () => {
    window.removeEventListener('resize', changeResize)
  }
}, [changeResize])

tips:
1. eslint-plugin-react-hooks 会默认给 hook 的依赖加上参数， 可能会导致程序出现bug, 最好关闭该条规则或设置为 warn状态
2. 一般用于: 请求数据, 添加监听逻辑(移除监听), 更新数据 ...


```

---

#### useRef

> 返回一个可变ref对象 function component 唯一引用, 并且自身发生改变是不会触发重新渲染

```js
function useRef<T>(initialValue: T): MutableRefObject<T>;
interface MutableRefObject<T> {
  current: T;
}

initialValue 默认值可以用 .current 进行访问

const scrollViewRef = useRef() -> scrollViewRef.current 进行使用

- 跳过首次执行
const useVaildExecute = (fn: () => (void | () => void), deps = []) => {
  const firstRun = useRef(false)
  useEffect(() => {
    if (firstRun.current) {
      firstRun.current = true
      return
    }
    fn()
  }, [...deps])
}


-  利用 useImperativeHandle , 父组件执行子组件的方法

const Child = (props) => {
  const gettest = () => 'test'
  useImperativeHandle(props.cRef, () => ({
    test: gettest,
  }))
  useEffect(() => {
    props.transCb(gettest)
  })
  return <View />
}
const Parent = () => {
  const parentRef = useRef({})
  const childRef = useRef({})
  const pressOK = () => {
    console.log(childRef.current.test(), 'useImperativeHandle')
    console.log(parentRef.current.test(), 'transCb')
  }
  const transCb = (cb) => {
    parentRef.current = {test: cb}
  }
  return (
    <TouchableOpacity onPress={pressOK}>
      <Text>dianwo</Text>
      <Child cRef={childRef} transCb={transCb} />
    </TouchableOpacity>
  )
}

tips:
1. 获取组件实例对象: 某些组件有提供ref api, 绑定后可以直接使用相应的api, 也可以利用 回调的方式或 useImperativeHandle 给父组件设置子组件的方法
2. 可用跨渲染变量: 来设置自己的逻辑, 并且ref发生改变时不会触发渲染， 临时变量, 定时器或延时器变量...
```

---

#### useCallback or useMemo

关于这2个hook 其实是较少使用的, 用的话是为了做性能优化但是

```js

useCallback: 缓存函数, 避免额外开销渲染
useCallback(fn, deps)

function useCallback<T extends (...args: any[]) => any>(callback: T, deps: DependencyList): T;


useMemo: 缓存变量, 避免额外开销渲染
useMemo(() => fn, deps)

function useMemo<T>(factory: () => T, deps: DependencyList | undefined): T;

```

个人理解: 

1. 频繁触发render,造成了性能瓶颈可以去缓存hook去优化组件
2. ***但是为了性能优化是有代价的, 不能什么场景下都去用这2个hook, 一般出现性能瓶颈后再去用这2个hook去优化会比较好***


---

#### useContext

- 上下文,跨组件传递数据

```js
function useContext<T>(context: Context<T>/*, (not public API) observedBits?: number|boolean */): T;
type Provider<T> = ProviderExoticComponent<ProviderProps<T>>;
type Consumer<T> = ExoticComponent<ConsumerProps<T>>;

interface Context<T> {
    Provider: Provider<T>;
    Consumer: Consumer<T>;
    displayName?: string;
}

type Cb = (_s: string) => void
const GlobalContext = React.createContext({
  name: 'ojbk',
  dispatch: ((_s: string) => void 0) as Cb,
})
const Grandfather = () => {
  const [name, setName] = useState('name1')
  return (
    <GlobalContext.Provider value={{name, dispatch: (s: string) => setName(s)}}>
      <Father />
    </GlobalContext.Provider>
  )
}
const Father = () => <Child />
const Child = () => {
  const globalContext = useContext(GlobalContext)
  return (
    <View>
      <Text onPress={() => globalContext.dispatch('okkk')} style={{padding: 40}}>
        {globalContext.name}
      </Text>
    </View>
  )
}

然后就可以实现一个类似的redux 的状态管理(当然上面的dispatch 本质上就是回调函数), 你也可以把useState 换成 useReducer更加贴合redux的使用场景
我们可以发现数据流的更新是要通过子组件进行触发, 父组件执行相应的hook update value 然后在使子组件的数据更新

tip:
1. 组件层级过深我们不想用 props 一层一层进行传递 可以用这个替代跨组件属性传递
2. 官方说明: 调用了 useContext 的组件总会在 context值变化时重新渲染 (只要 Context.Provider 的value值发生改变时,使用该hook useContext(Context) 的组件就会重新触发render)
3. 由于自身变化会导致引用了的子组件中会重复渲染, 所以搭配的时候谨慎使用

到这里我提一句: redux or mobx 他们在使用的时候会有个 连接库: react-redux or mobx-react 当数据流更新时 触发重新渲染操作是在这里面触发的, 我们上面的 setName 也是这个触发重新更新的操作
```

---


#### useLayoutEffect

- 功能与 useEffect 类似,参数也一样

```js
function useLayoutEffect(effect: EffectCallback, deps?: DependencyList): void;

// eg: 渲染app头部 同步去update
export const useReWriteHeader = (props: RewriteProps, deps: any = []) => {
  const navigation = useNavigation()
  useLayoutEffect(() => {
    const options: RewriteProps = {}
    if (props.headerTitle) {
      options.headerTitle = props.headerTitle
    }
    if (props.headerRight) {
      options.headerRight = () => props.headerRight!()
    }
    if (props.headerLeft) {
      options.headerLeft = () => props.headerLeft!()
    }
    if (props.header === null) {
      options.header = null
    }
    navigation.setOptions(options)
  }, [navigation, ...deps])
}

tips:
1. 执行时机: useLayoutEffect 是比 useEffect 先执行, useLayoutEffect是触发渲染同步执行, 而useEffect是渲染完成后才执行
2. 官方说明: useEffect以避免阻塞视觉更新， 如果是更新dom等操作优先 useLayoutEffect , request数据等操作的话就用useEffect

```
---

#### useReducer

```js
function useReducer<R extends ReducerWithoutAction<any>>(
    reducer: R,
    initializerArg: ReducerStateWithoutAction<R>,
    initializer?: undefined
): [ReducerStateWithoutAction<R>, DispatchWithoutAction];

type ReducerWithoutAction<S> = (prevState: S) => S;

const loginReducer = (state: any, action: any) => {
  switch (action.type) {
    case 'login_success':
      return {...state, status: 'success'}
    case 'login_out':
      return {...state, status: 'logout'}
    default:
      return {...state, status: 'init'}
  }
}

const App = () => {
  const [loginRes, dispatch] = useReducer(loginReducer, {status: 'ooo'})
  return <Text style={{padding: 40}} onPress={() => dispatch({type: 'login_successa'})}>当前状态是{loginRes.status}</Text>
}

当状态比较复杂时可用 useReducer 来代替 useState

```

---

### 自定义hook

1. 5e-hooks 常用的自定义hook

```
useMount
useResetAction
useDebounce
useThrottle
useSessionStorage
useLocalStorage
useOnresize
useReWriteHeader
useFocus

当然自定义hook的意义就是去抽离一些公用的逻辑
1. useXx use打头
2. 顶层调用
```

---

### 源码详解

```
后续的篇章会介绍深入的源码
```

---

### 参考文章

1. [react-hook 官方website](https://react.docschina.org/docs/hooks-reference.html)
2. [用 useContext + useReducer 替代 redux](https://juejin.im/post/6844903854807482382#heading-9)