---
title: "Javascript Closure"
date: 2022-05-01T01:46:46+08:00
draft: false
url: /posts/javascript-closure
tags: [前端面試練習]
---

***
計畫把常被問到的前端面試問題自己消化過後發上來！
有錯的話還請留言多多指教QQ
***

## Closure
### return a function
傳進去的變數會記在這個 function 的 scope 裡，下次 call 的時候繼續使用。
```javascript
function aFunc(x){
  function bFunc(){
    console.log( x++ )
  }
  return bFunc
}

const newFunc = aFunc(1)
newFunc() //1
newFunc() //2
```
簡單的 closure 錯誤
```javascript
const varGlobal = 'x'

function outer(paramOuter){
  const varOuter ='y'

  function inner(paramInner){
    const varInner ='z'

    //print
    console.log(varGlobal)
    console.log(varOuter)
    console.log(varInner)
    console.log(paramOuter)
    console.log(paramInner)
  }
  
  // console.log(varInner) will cause ReferenceError

  return inner
}

const func = outer('a')
func('b')
//x y z a b
```
### setTimeOut + closure
```javascript
function counter() {
    let i = 0
    for (i = 0; i< 5; i++) {
        setTimeout(function() {
            console.log('counter is ' + i)
        }, 1000)
    }
}

counter() // 一秒後 五次 counter is 5
```
setTimeOut 會先進到 call stack 裏 但同時 for loop 已經運算完成，所以會在一秒後 log 出 ５個 5。
