---
title: "Javascript 基礎比較"
date: 2022-04-29T21:43:53+08:00
draft: false
url: /posts/javascript-basic-compare
tags: [前端面試練習]
---
***
計畫把常被問到的前端面試問題自己消化過後發上來！
有錯的話還請留言多多指教QQ
***

## null vs undefined
一個變數要是 null 必須要被主動給予 null。
若宣告變數時沒有賦值，則該變數為 undefined

## Equality === vs ==
`==` 檢查值是否相等，不檢查型態。**比較時會進行型態轉換**。

```
4 == 4 //true
'4' == 4 //true
0 == false // true
```

`===` 檢查**值**及**型態**是否相等，不進行型態轉換。
```
4 === 4 //true
'4' === 4 //false
0 === false // false
```

## Primitive Vs Non-Primitive

### Primitive Type - handle by value
String, Number, Boolean, Null, Undefined

變數存的是實際的值
```javascript
let a = 1
let b = a
a = 2
console.log(b) // 1
```

### Non-Primitive - handle by reference
Object, Function, Array, Set

變數存的是值的記憶體位置
```javascript
let a = [1, 2]
let b = a
a = [1, 2, 3]
console.log(b) // [1, 2, 3]

// 若要複製值的話則要用 ...
let a = [1, 2]
let b = [...a]
a = [1, 2, 3]
console.log(b) // [1, 2]

// 比較
let a =[1, 2]
let b =[1, 2]
a === b // false 因為兩者的記憶體位置不同
```
