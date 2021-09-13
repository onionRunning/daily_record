# 关于stomp协议

## 基础

STOMP( Simple Text-Orientated Messaging protocol) 面向消息的简单文本协议

STOMP 是一种基于帧 frame的协议:

- 一个命令command
- 一组可选头部 header
- 一个可选主体 body 是字符串形式

```md
STOMP 在 WebSocket 之上提供了一个基于帧的线路格式层

Http 与Tcp套接字添加请求-响应模型层一样

1. 默认开启了心跳机制

client.heartbeat.outgoing = 10000
client.heartbeat.incoming = 0

2. 相比WebSocket 更加高级的语义协议

3. 订阅-取消订阅的方式更加灵活


// 可以支持传输过程的 debug信息 以 console.log的形式在 客户端浏览器中打印出来
```

## react + hook

```js
import SockJS from 'sockjs-client'
import Stomp, {Client} from 'stompjs'

import {useEffect, useRef, useState} from 'react'

interface Props {
  url?: string
  address?: string
}

interface Info {
  status: string
  content?: any
}

const useStompSocket = (props: Props): [Info, (address: string) => void, (address: string) => void] => {
  const client = useRef<Client>(null)
  // 连接状态
  const [connectInfo, setConnectInfo] = useState<Info>({status: 'init', content: ''})
  const subscriptions = useRef(new Map())
  // 初始化客户端
  const initClient = () => {
    if (client.current) return
    const ws = new SockJS( props.address || 'https://apitest.wemore.com/foxtable/webSocket')
    client.current = Stomp.over(ws)
    client.current.debug = null
  }
  // 开始连接
  const connect = () => {
    client.current.connect({}, () => subscribe(props.address), error)
  }

  useEffect(() => {
    if (!props.address) return
    initClient()
    connect()
    return () => {
      setConnectInfo({status: 'init'})
    }
  }, [props.address])
  // 失败的逻辑处理
  const error = err => {
    console.error('连接失败', err)
    setConnectInfo({status: 'fail', content: err})
    tryConnect()
  }
  // 重连
  const tryConnect = () => {
    const afterConnect = () => {
      initClient()
      connect()
    }
    disConnect(afterConnect)
  }
  // 开始订阅
  const subscribe = (address: string) => {
    const sub = client.current.subscribe(address, data => {
      setConnectInfo({status: 'success', content: JSON.parse(data?.body)})
    })
    subscriptions.current.set(address, sub)
  }
  // 取消订阅
  const unsubscribe = (address: string) => {
    const sub = subscriptions.current?.get(address)
    sub?.unsubscribe?.()
    subscriptions.current?.delete(address)
  }
  // 取消连接
  const disConnect = (cb?: () => void) => {
    client.current.disconnect(() => {
      cb?.()
      client.current = null
    })
  }
    // 添加订阅
  const addSubscribe = (newAddress: string) => {
    if (client.current && connectInfo.status === 'success') {
      subscribe(newAddress)
    }
  }
  return [connectInfo, unsubscribe, addSubscribe]
}

export default useStompSocket

```

## 参考文章

1 [STOMP和WebSocket](https://blog.csdn.net/m0_37542889/article/details/83750665)