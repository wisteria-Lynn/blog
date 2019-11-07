---
title: ES6新特性
comments: true
date: 2019-10-28 08:46:43
categories:
 - es6
 - js
tags:
img:
---

## let,const

* let

  * let定义变量不可以再次定义,可以从新赋值
  * 具有块级作用域
  * 没有变量提升,必须先定义在使用
  * let声明的变量不能被window调用,是独立的
  
* const

  * const定义常量,是不可变的,一旦定义,不能修改其值
  * 初始化常量时,必须给初始值
  * 具有块级作用域
  * 没有变量提升,必须先定义在使用
  * const声明的常量也是独立的,不能被window调用

> var
> 变量提升，可被window调用，可以不给初始值，具有全局作用域

## 解构赋值

* 数组解构

  ```js
// 1.变量和值一一对应
let arr = [1, 2, 3];
let [a, b, c] = arr;
console.log(a,b,c);   // 1 2 3
```

  ```js
// 2. 变量多,值少
let arr = [1, 5, 8];
let [a, b, c, d] = arr;
console.log(a, b, c, d); // 1 5 8 undefined
```

  ```js
// 3. 变量少,值多
let arr = [5, 9, 10, 8, 3, 2];
let [a, b] = arr;
console.log(a, b);  // 5, 9
```

  ```js
// 4.按需取值
let arr = [5, 9, 10, 8, 3, 2];
let [, , a, , b] = arr; // 不需要用变量接收的值，用空位占位
console.log(a, b); // 10, 3 
```

  ```js
// 5.剩余值
let arr = [5, 9, 10, 8, 3, 2];
let [a, b, ...c] = arr; // ...c 接收剩余的其他值，得到的c是一个数组
console.log(a, b, c); 
// 结果：
// a = 5, 
// b = 9, 
// c = [10, 8, 3, 2]
```

  ```js
// 6.复杂的情况,只要符合模式.即可解构
let arr = ['zhangsan', 18, ['175cm', '65kg']];
let [, , [a, b]] = arr;
console.log(a, b); // 175cm 65kg
```

* 对象解构

  ```js
// 1. 变量名和属性名一样
let { foo, bar } = {foo: 'aaa', bar: 'bbb'};
console.log(foo, bar); // aaa, bbb

let {a, c} = {a: 'hello', b: 'world'};
console.log(a, c); // hello, undefined
```

  ```js
// 2. 通过 :来更改变量名
let {a, b:c} = {a: 'hello', b: 'world'};
console.log(a, c); // hello, world
```

  ```js
// 3. 变量名和属性名一致即可获取到值,不需要一一对应,无需按顺序
let {b,a} = {a: 'hello', b: 'world'};
console.log(b); // world
console.log(a); // hello
```

  ```js
// 4. 剩余值
let obj = {name:'橘右京', age:20, gender:'男'};
let {name, ...a} = obj;
console.log(name, a);  // name = zs   a = {age: 20, gender: "男"};
```

  ```js
// 5. 复杂情况,只要符合模式,即可解构
let obj = {
name: '不知火舞',
age: 22,
dog: {
    name: '娜可露露',
    age: 13
    }
};
let {dog: {name, age}} = obj;
console.log(name, age); // 娜可露露 13
```

> dog不会被赋值，只有最后的对象才会被赋值

```js
// 假设服务器上的获取的数据如下
let res = {
    data: ['a', 'b', 'c'],
    meta: {
        code: 200,
        msg: '获取数据成功'
    }
}
// 如何获取到 code 和 msg
let { meta: { code, msg } } = res;
console.log(code, msg); // 200, 获取数据成功
```
## 函数

* 箭头函数（往下讲）

* 函数参数的默认值

```js
// ES5 中给参数设置默认值的变通做法
function fn(x, y) {
    y = y || 'world';
    console.log(x, y);
}
fn(1)
// ES6 中给函数设置默认值
function fn(x, y = 'world') {
    console.log(x, y);
}
fn(2)
fn(2,3)
```

* rest参数

剩余参数,以...修饰最后一个参数,把多余的参数放到一个数组中

  ```js
// 参数很多，不确定多少个，可以使用剩余参数
function fn(...values) {
    console.log(values); // [6, 1, 100, 9, 10]
}
// 调用
console.log(fn(6, 1, 100, 9, 10));
```
>rest参数只能是最后一个参数

## 箭头函数

* 箭头函数的特点

  * 箭头函数内部的this指向外部作用域中的this,箭头函数没有自己的this
  * 箭头函数中内部没有arguments
  * 箭头函数不能作为构造函数



  ```js
let a = ()=>{}
// 等价于
function a(){

}
```

## 内置对象的扩展

* array的扩展

  * 扩展运算符(...可以把数组中的每一项展开)
  
```js
// 合并两个数组
let arr1 = [1, 2];
let arr2 = [3, 4];
let arr3 = [...arr1, ...arr2];
console.log(arr3); // [1, 2, 3, 4]
```

* Array.from()

  * 把伪数组转换成数组
  * 伪数组必须有length属性,没有length得到一个空数组
  * 转换后的数组长度根据伪数组的length决定的
  
```js
let fakeArr = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
};
let arr = Array.from(fakeArr);
console.log(arr); // ['a', 'b', 'c']
```