# Buffer相关 笔记

> 二进制对像

```md
> 这些是常见的概念 我们需要理解这些东西
- ArrayBuffer
- Uint8Array
- Uint16Array
- Uint32Array
- TypedArray
- Blob
- DataView
```

## ArrayBuffer

> **最基础的二进制对象, 对固定长度连续内存空间的引用**

无法直接操作, 需要通过视图进行操作

Uint8Array, Uint16Array, Uint32Array 就是视图也是 ArrayBuffer的翻译器

Uint8Array 每个字节视为一个单位     // 255

Uint16Array 2个字节视为一个单位     // 65535

Uint32Array  4个字节为一个单位     // 4294967295

```ts
const buf8 = new Uint8Array()
const buf16 = new Uint16Array()
const buf32 = new Uint32Array()
```

以上3种格式统称: TypedArray

## Blob

> **浏览器提供承载原始数据的二进制对象， Blob 可以位于磁盘、高速缓存内存和其他不可用的位置**

```md

使用场景

1. 分片上传 (使用slice方法)


2. 储存下载数据

    ```ts
        axios.get('https://xxxxxx', {responseType: 'blob'})
        .then(res => {
            let url = URL.createObjectURL(res.data)
            let a = document.createElement('a')
            a.setAttribute('download', '图片')
            a.href = url
            a.click()
        })
    ```

3. Blob 转 Base64 

[参考链接](https://zhuanlan.zhihu.com/p/57700185)

4. Blob 与 ArrayBuffer 互转
    
    ```ts
        // Blob ----> ArrayBuffer
        let blob = new Blob([1,2,3,4])
        let reader = new FileReader()
        reader.onload = function(result) {
            console.log(result)
        }
        reader.readAsArrayBuffer(blob)

        // ArrayBuffer ----> Blob
        const blob = new Blob([buffer])
    ```

5. blob与复制粘贴

```

## DataView

> **DataView 是一种底层的更灵活读取 ArrayBuffer的视图**

```js
const buffer = new ArrayBuffer(16)
const view = new DataView(buffer)
```

## ArrayBuffer / Uint8Array / DataView

> 到这里我们基本上了解了以上的相关信息, 蓝牙通信通过ArrayBuffer格式 ArrayBuffer 与字符串进行互转

```ts
    // ArrayBuffer ---> 转 utf-8
    const uintToString = (uintArray) => {
        let encodedString = String.fromCharCode.apply(null, uintArray)
        return decodeURIComponent(escape(encodedString))
    }

    // utf8 ---> ArrayBuffer

    const stringToUint = (string) => {
        let string = btoa(unescape(encodeURIComponent(string))),
            charList = string.split(''),
            uintArray = [];
        for (var i = 0; i < charList.length; i++) {
            uintArray.Push(charList[i].charCodeAt(0))
        }
        return new Uint8Array(uintArray)
    }

// ---

    // ArrayBUffer ---> 转 utf-16
    const ab2hex = buffer => {
        const hexArr = Array.prototype.map.call(new Uint8Array(buffer), bit => {
            return ('00' + bit.toString(16)).slice(-2)
        })
        return hexArr.join('')
    }

    // 16进制转 ArrayBuffer
    const hex2ab = (hex) => {
        const typedArray = new Uint8Array(hex.match(/[\da-f]{2}/gi).map(i => {
            return parseInt(i, 16)
        }))
        return typedArray.buffer
    }


    // ArrayBuffer <----> string

    function ab2str(buf) {
        return String.fromCharCode.apply(null, new Uint16Array(buf));
    }
    function str2ab(str) {
        const buf = new ArrayBuffer(str.length * 2)
        const bufView = new Uint16Array(buf);
        for (let i = 0, strLen=str.length; i < strLen; i++) {
            bufView[i] = str.charCodeAt(i);
        }
        return buf;
    }


```

## 参考文章

1. [Blob-对象介绍](https://zhuanlan.zhihu.com/p/161000123)

2. [我妈都看得懂的 Buffer 基础](https://juejin.cn/post/6911487429471895560#heading-11)

3. [url、base64、blob相互转换方法](https://zhuanlan.zhihu.com/p/57700185)