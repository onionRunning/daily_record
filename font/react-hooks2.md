### react-hook2

> 在上一篇我们差不多了解了基础hook的用法以及使用过程的一些tips, 本篇会从源码上对基础hook进行分析 hook的工作原理

***源码版本号: 17.0.0***

#### useState

```ts
export function useState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  const dispatcher = resolveDispatcher();
  return dispatcher.useState(initialState)
}

function resolveDispatcher() {
  return ReactCurrentDispatcher.current
}

const ReactCurrentDispatcher = {
  current: null
}

what 这就没了?   useState ---> ReactCurrentDispatcher.current.useState 

核心: ReactCurrentDispatcher

```






参考文章：
  1. [react 源码](https://github.com/facebook/react)   