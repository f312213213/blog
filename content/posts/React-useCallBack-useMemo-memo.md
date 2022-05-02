---
title: "UseCallBack UseMemo Memo"
date: 2022-05-03T01:43:21+08:00
draft: false
url: /posts/react-useCallBack-UseMemo-memo
tags: [前端面試練習]
---
***
計畫把常被問到的前端面試問題自己消化過後發上來！
有錯的話還請留言多多指教QQ
***

這題面試一直被問到，每次都講不好，下定決心研究一番
### useCallback vs useMemo vs memo
#### memo
用法：把 functional component 包起來，比較從外部傳進去的 props 是否相等，有改變才會 rerender。

目地：防止 component 在 props 一樣的狀況下 rerender

但只進行 shallow compare，若傳入的是 Non-Primitive 則需多帶一個參數 areEqual
```javascript
state = {
  title: '',
  releaseDate: ''
}
function qreEqual(prevState, nextState) {
  return prevState.title === nextState.title
    && prevState.releaseDate === nexnextStatetMovie.releaseDate;
}
// usage
React.memo(Component, qreEqual);
```

#### useCallback
用法：把 function 包起來，當 dependency array 裡的值改變了，才會重新執行 function.

目地：防止 function 在 不相干的變數改變時，依舊重新執行。

```javascript
const sumContentUseCallback = useCallback(() => {
  console.log('運算')
  let sum = 0
  for (let i = 0; i < content2; i++) {
    sum += i
  }
  return sum
}, [content2])
// sumContentUseCallback is a funciton
// 用的時候這樣用
export const AddCallback = ({ sumContentUseCallback }) => {
  const [sum, setSum] = React.useState(0)
  React.useEffect(() => {
    setState(sumContentUseCallback())
  }, [complete2])
  return (
      <div>
        useCallback 累加：{sum}
      </div>
  )
}
```

#### useMemo
用法：把 function 包起來，當 dependency array 裡的值改變了，才會重新執行 function，並獲得一個新的值。

目地：防止 function 在 不相干的變數改變時，依舊重新執行。

```javascript
const sumContentUseMemo = useMemo(() => {
  console.log('運算')
  let sum = 0
  for (let i = 0; i < content2; i++) {
    sum += i
  }
  return sum
}, [content2])
// sumContentUseMemo is a value
// 用的時候這樣用
export const AddMemo = ({ complete2 }) => {
  return (
      <div>
        useMemo 累加：{complete2}
      </div>
  )
}
```

##### useCallback vs useMemo
兩個參數一模一樣，差異在於 useCallback return function, useMemo return value

useCallback -> function

useMemo -> value
