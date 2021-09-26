# sort

数组里的元素按照大小进行排序

// 比较排序

- 冒泡算法
- 快排算法

- 插入排序
- 希尔排序

- 选择排序
- 堆排序

- 归并排序

// 非比较排序

- 计数排序
- 桶排序
- 基数排序

## 冒泡排序

- 比较相邻的2个元素, 然后按照大小顺序 是否交换相邻元素的位置

## 选择排序

- 选择一个元素 与当前所有的元素比较 遇到小的就交换位置, 然后继续比较下去，

## 插入排序

- 默认第一个元素已经排序, 后面的元素从已排序的最后一位开始往前比较, 如果大于最后一个元素直接 位置不变， 但是如果小于已经排序 后面的元素 就往前推直到大于排序的元素 或者直接到达第一位

```go
package main

import "fmt"

func main() {
 var a = []int{1, 10, 5, 2, 7, 2, 15}
 c := bubbleSort(a)
 d := selectionSort(a)
 e := insertionSort(a)
 fmt.Println(c)
 fmt.Println(d)
 fmt.Println(e)
}

// 冒泡排序
func bubbleSort(arr []int) []int {
 for a := 0; a < len(arr)-1; a++ {
  for b := 0; b < len(arr)-1-a; b++ {
   if arr[b] > arr[b+1] {
    c := arr[b]
    arr[b] = arr[b+1]
    arr[b+1] = c
   }
  }
 }
 return arr
}

// 选择排序
func selectionSort(arr []int) []int {
 for a := 0; a < len(arr)-1; a++ {
  for b := a + 1; b < len(arr); b++ {
   if arr[a] > arr[b] {
    t := arr[a]
    arr[a] = arr[b]
    arr[b] = t
   }
  }
 }
 return arr
}

// 插入排序
func insertionSort(arr []int) []int {
 for a := 1; a < len(arr); a++ {
  pre := a - 1
  current := arr[a]
  for ; pre > 0 && current < arr[a]; pre-- {
   arr[pre+1] = arr[pre]
  }
  arr[pre+1] = current
 }
 return arr
}

```

```js

const bubbleSort = (arr) => {
  for (let i =0; i < arr.length -1; i++) {
    for (let j =0; j < arr.length -1 -i; j++) {
        if (arr[j] > arr[j+1]) {
            const t = arr[j]
            arr[j] = arr[j+1]
            arr[j+1] = t
        }
    }
  }
  return arr  
}
const testDemo = [12, 4, 8, 1, 4, 9]
console.log(bubbleSort(testDemo), 'x')

// 循环一轮只能得到 一个最大或最小的值, 需要双重循环 得到所有的 排序量

const selection = (arr) => {
    for(let i=0; i < arr.length -1; i++) {
        for(let j=i+1; j < arr.length; j++) {
            if (arr[i] > arr[j]) {
                const t = arr[i]
                arr[i] = arr[j]
                arr[j] = t
            } 
        }       
    } 
    return arr     
}

const testDemo = [12, 4, 8, 1, 4, 9]
console.log(selection(testDemo), 'x')

const insertionSort = (arr) => {
  for (let i= 1; i< arr.length; i++) {
      let pre = i - 1
      let current = arr[i]
      //   while(pre >= 0 && current < arr[pre]) {
      //       arr[pre + 1] = arr[pre]
      //       pre--
      //   }
      for (; pre >= 0 && current < arr[pre]; pre--) {
        arr[pre + 1] = arr[pre]
      }
      arr[pre + 1] = current
  }
  return arr
}

const testDemo = [12, 4, 8, 1, 4, 9]
console.log(insertionSort(testDemo), 'x')

```
