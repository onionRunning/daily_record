# websocket

## 背景

```md
1. websocket是html5 提供的TCP连接上的全双工通讯的协议! 浏览器与客户端完成一次握手创建持久连接 进行双向数据传输!

2. Ajax轮询: 长短轮询 

3. 长连接Tcp连接 http的 Keep-alive

  connection: keep-alive
  keep-Alive: timeout=2000000
```

## api

```js
const ws = new WebSocket(url [, protocols])

// url: websocket 服务器响应的URL
// 默认字协议

// 关闭时触发
ws.onclose = (event) => {
  console.log("WebSocket is closed now.")
}

// ws.addEventListener('close', () => {})

// 错误时触发
ws.onerror = (event) => {
  console.error("WebSocket error observed:", event)
}
// ws.addEventListener('error', () => {})


// 接受消息时触发
ws.onmessage = (event) => {
  console.debug("WebSocket message received:", event.data)
}
// ws.addEventListener('message', () => {})

// 连接成功时触发
aWebSocket.onopen = (event) => {
  console.log("WebSocket is open now.");
}
// ws.addEventListener('open', () => {})


// websocket 的连接状态
ws.readyState

0 ---- CONNECTING 创建但尚未连接
1 ---- OPEN       已经连接并且准备通信
2 ---- CLOSING    连接正在关闭
3 ---- CLOSED     连接已经关闭


// 关闭websocket
ws.close()


// 发送消息
ws.send(data)

// USVString  ArrayBuffer Blob ArrayBufferView

```

## 项目中的使用

```ts
import {getJsonString} from '@/global/utils'
import {useState, useRef, useEffect} from 'react'

interface SocketParams {
  url: string
  isStart?: boolean
}

interface SocketRes {
  wsData: any
  state?: number
  closeWebSocket?(): void
  reconnect?(): void
}

const useWebsocket = (payload: SocketParams): SocketRes => {
  const {url, isStart} = payload
  const ws = useRef(null)
  const timer = useRef(null)
  const [wsData, setMessage] = useState('')

  const createWebSocket = () => {
    if (ws.current) return
    try {
      ws.current = new WebSocket(url)

      ws.current.onopen = () => {
        // 开始发送心跳包
        timer.current = setInterval(() => {
          sendHeartBeatPackage('')
        }, 3000)
      }
      // 关闭重连
      ws.current.onclose = () => {
        // reconnect()
        console.error('close', ws?.current?.readyState)
      }
      // 错误重连
      ws.current.onerror = e => {
        // 重连
        reconnect()
      }
      ws.current.onmessage = e => {
        setMessage(getJsonString(e.data))
      }
    } catch (error) {
      console.error(error)
    }
  }

  const webSocketInit = () => {
    if (!ws.current || ws.current.readyState === 3) {
      creatWebSocket()
    }
  }

  //  关闭 WebSocket
  const closeWebSocket = () => {
    if (ws.current && ws.current?.close) {
      try {
        ws.current?.close()
      } catch (error) {
        console.error(error)
      }
    }
  }

  // 发送心跳包
  const sendHeartBeatPackage = (info?: any) => {
    if (ws.current && ws.current?.send) {
      try {
        ws.current?.send(info)
      } catch (error) {
        console.error(error)
      }
    }
  }

  const reconnect = () => {
    try {
      closeWebSocket()
      // ws.current = null
      createWebSocket()
    } catch (e) {
      console.error(e)
    }
  }

  useEffect(() => {
    if (isStart) {
      webSocketInit()
    }
    return () => {
      closeWebSocket()
      ws.current?.close()
      ws.current = null
      clearInterval(timer.current)
    }
  }, [ws, isStart])

  return {wsData, closeWebSocket}
}
export default useWebsocket

```

## 一些问题

1. 会断连，客户端发送心跳包

2. https域名下 需要用wss协议进行连接否则失效

## 参考连接

1. [WebSockets](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSockets_API)
2. [零距离接触websocket🚀](https://juejin.cn/post/6876301731966713869)