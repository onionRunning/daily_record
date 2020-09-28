## react-hook

### 常用api hook 以及用法

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

关于这2个hook 其实是较少使用的, 用的话是为了做性能优化 

```js

useCallback: 缓存函数, 避免额外开销渲染
useCallback(fn, deps)

function useCallback<T extends (...args: any[]) => any>(callback: T, deps: DependencyList): T;


useMemo: 缓存变量, 避免额外开销渲染
useMemo(() => fn, deps)

function useMemo<T>(factory: () => T, deps: DependencyList | undefined): T;

```

---

#### useContext

---


#### useLayoutEffect

---




### 自定义hook



---

### 源码详解

---

### 参考文章

1. [react-hook 官方website](https://react.docschina.org/docs/hooks-reference.html)