---
title: arguments
comments: true
date: 2019-10-29 09:11:55
categories:
 - js
tags:
img:
---

arguments 是一个对应于传递给函数的参数的类数组对象。

__语法__

arguments对象是所有（非箭头）函数中都可用的局部变量。你可以使用arguments对象在函数中引用函数的参数。此对象包含传递给函数的每个参数，第一个参数在索引0处。

```js
// 列如：
function func(a,b,c){
    console.log(arguments[0])// a
    console.log(arguments[1])// [1,2]
    console.log(arguments[2])// ()=>{}
}
func('a',[1,2],()=>{})
```

arguments对象不是一个 Array 。它类似于Array，但除了length属性和索引元素之外没有任何Array属性。例如，它没有 pop 方法。但是它可以被转换为一个真正的Array：

```js
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);

// ES2015
const args = Array.from(arguments);
const args = [...arguments];
```

__类型__

```js
// 只能在函数内部使用
console.log(typeof arguments) // undefined 
function test(a){
    console.log(a,Object.prototype.toString.call(arguments));
    // 1,'object','[object Arguments]'
    console.log(arguments[0],arguments[1]);// 1 undefined
    console.log(typeof arguments[0]);// number
}
test(1);
```