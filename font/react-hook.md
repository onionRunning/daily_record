## react-hook

### 常用api hook 以及用法

#### useState

> 我们用它来更新组件, 自定义hook数据

```js
function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>];

tips: 
1. initialState: 可以为默认值, 也可以返回默认值的函数, useState('test') or useState(() => 'test')
2. 如果内部需要的状态比较多，可以直接设置对象集合代替单个属性

// bad
const [isLoading, setLoading] = useState(false)
const [isShowModal, setShowModal] = useState(false)
const [currentPage, setCurrentPage] = useState(1)
const [list, setList] = useState([])
...

// good
const [goodStatusParams, setGoodStatusParams] = useState({
    isLoading: false,
    isShowModal: false,
    currentPage: 1,
    list: []
})

// 当然不是什么操作都需要这么处理, 
// 如果你的组件内部逻辑比较简单，推荐前者直观, 后者只是一定程度上减少了 setState 的个数
// 后者可以整合某一类的参数聚合, 新加状态属性时， 只用添加字段 而不是一直堆useState


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

// 记录一下, 后续源码分析的时候用上.
```


#### useEffect

> 生命周期替代者

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
})


tips:
1. eslint-plugin-react-hooks 会默认给 hook 的依赖加上参数， 可能会导致程序出现bug, 最好关闭该条规则或设置为 warn状态

// 点击加号后， resize 打印count 是啥
const App = () => {
  const [count, setCount] = useState(0)
  useEffect(() => {
     window.addEventListener('resize', handleResize)
     return () => window.removeEventListener('resize', handleResize)
  }, [])
  const handleResize = () => {
    console.log(`count is ${count}`)
  }
  return (
    <div className="App">
      <button onClick={() => setCount(count + 1)}> + </button>
      <h1>{count}</h1>
    </div>
  );
}

2. 多个useEffect 执行, 如果没有依赖变化时，只会重新render一遍？

// useEffect 机制？
```


---

### 自定义hook

---

### 源码详解

---

### 参考文章
