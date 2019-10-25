---
title: 常见js面试问题
comments: true
date: 2019-10-24 14:11:42
categories:
 - 面试
 - js
tags:
img:
---

## null undefined NaN "" 的区别

null

* 是一种特殊的object
* 空值，空指针，指针未指向任何内存空间（指向不存在的东西）
* 可以与undefined相等 == ，但 === 时不相等
* 可以等于自己，返回true
* 转化为数值是0，布尔值是false，字符串是'null'
* 与false比较或严格比较，输出false 

undefined

* 对应类型undefined
* 全局作用域的一个变量
* 空值，未定义，使用了一个并未声明的变量时，或者使用了已经声明但还没有赋值的变量时，又或者使用了一个并不存在的对象属性时，返回这个值
* 可以等于自己，返回true
* 转为数值是NaN，布尔值是false，字符串是'undefined'
* 与false比较或严格比较，输出false 

NaN

* 一种特殊的Number,表示非数字值，实际上也是一个数字（typeof NaN = "number"）
* NaN不等于自己(可以用isNaN()检测)
* 转为数值是NaN，布尔值是false，字符串是'NaN'
* 与false比较或严格比较，输出false 

""

* 字符串类型，不等于空格
* 空字符串，长度为0
* 转为数值是0，布尔值是false，字符串是""
* 与false比较时输出true，严格比较时，输出false

## 完整的登陆流程

登陆页面，点击登录按钮时触发的事件

* 客户端请求后台接口
* 后台验证通过，保存用户的登陆状态，并写入cookie中
* 用户进入需要登录权限的页面时，判断该状态，确认用户是否需要重新登录
* 如cookie过期，跳转至登录页重新认证

## new操作符具体干了什么

new的实现原理是什么？

```
let func = new Func()
```

* 创建一个空对象obj

```
let obj = new Object()
```

* 链接到原型

让obj对象的__proto__指向构造函数Func的原型
此时便建立了 obj 对象的原型链：obj->Func.prototype->Object.prototype->null

```
obj.__proto__ = Func.prototype
```

* 绑定this值

让Func中的this指向obj对象

```
Func.call(obj)
```

* 返回obj对象

```
func = obj
```

## 数组去重方法

* 双重for循环

* filter + indexOf

```
arr.filter((item, index)=> {
   return arr.indexOf(item) === index
})
```

* ES6 new Set()

```
function distinct(arr) {
    return Array.from(new Set([...arr]))
}
```

* for...of + Object

```
function distinct(arr) {
    let result = []
    let obj = {}
    for (let i of arr) {
        if (!obj[i]) {
            result.push(i)
            obj[i] = 1
        }
    }

    return result
}
```

