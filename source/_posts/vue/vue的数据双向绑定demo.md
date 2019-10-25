---
title: vue的数据双向绑定demo
comments: true
date: 2019-10-25 08:59:48
categories:
 - vue
tags:
img:
---

目前的几种主流前端框架中，`react`是单向绑定，而angular.js和vue.js是双向绑定，实现双向绑定的方法有：
1发布者-订阅者模式（backbone.js）
2.脏值检查（angular.js）
3.数据劫持（vue.js）

脏值检查：angular.js 是通过脏值检测的方式比对数据是否有变更，来决定是否更新视图，最简单的方式就是通过 setInterval() 定时轮询检测数据变动，当然Google不会这么low，angular只有在指定的事件触发时进入脏值检测，大致如下：
1.DOM事件，譬如用户输入文本，点击按钮等。( ng-click )
2.XHR响应事件（http）
3.浏览器Location变更事件
4.Timer事件

数据劫持： vue.js 则是采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

所以，要实现mvvm的双向绑定，就必须实现以下几点：
1.实现一个数据监听器Observer，能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知订阅者
2.实现一个指令解析器Compile，对每个元素节点的指令进行扫描和解析，根据指令模板替换数据，以及绑定相应的更新函数
3.实现一个Watcher，作为连接Observer和Compile的桥梁，能够订阅并收到每个属性变动的通知，执行指令绑定的相应回调函数，从而更新视图
4.mvvm入口函数，整合以上三者

## （一）实现Observer

我们知道可以利用Obeject.defineProperty()来监听属性变动，那么将需要observe的数据对象进行递归遍历，包括子属性对象的属性，都加上 setter和getter这样的话，给这个对象的某个值赋值，就会触发setter，那么就能监听到了数据变化。

## （二）实现Compile

   Compile主要做的事情是解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图，

因为遍历解析的过程有多次操作dom节点，为提高性能和效率，会先将跟节点el转换成文档碎片fragment进行解析编译操作，解析完成，再将fragment添加回原来的真实dom节点中。

## （三）实现Watcher

Watcher订阅者作为Observer和Compile之间通信的桥梁，主要做的事情是:
1.在自身实例化时往属性订阅器(dep)里面添加自己
2.自身必须有一个update()方法
3.待属性变动dep.notify()通知时，能调用自身的update()方法，更新视图

实例化Watcher的时候，调用get()方法，通过Dep.target = watcherInstance标记订阅者是当前watcher实例，强行触发属性定义的getter方法，getter方法执行的时候，就会在属性的订阅器dep添加当前watcher实例，从而在属性值有变化的时候，watcherInstance就能收到更新通知。

## （四）实现MVVM

MVVM作为数据绑定的入口，整合Observer、Compile和Watcher三者，通过Observer来监听自己的model数据变化，通过Compile来解析编译模板指令，最终利用Watcher搭起Observer和Compile之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定效果。

## （五）实现一个数据双向绑定demo

```html
<!DOCTYPE html>
<html>
<head></head>
<body>
    <div id="app">
        <input type="text" id="a" v-model="text">
        {{text}}
    </div>
<script type="text/javascript">
// 模板解析
function Compile (node, vm) {
    if (node) {
        this.$frag = this.nodeToFragment(node, vm)
        return this.$frag
    }
}
Compile.prototype = {
    nodeToFragment: function (node, vm) {
        var self = this
        var frag = document.createDocumentFragment()
        var child
        while (child = node.firstChild) {
            self.compileElement (child, vm)
            frag.append(child) // 将所有子节点添加到fragment中
        }
        return frag
    },
    compileElement: function (node, vm) {
        var reg = /\{\{(.*)\}\}/
        // 节点类型为元素
        if (node.nodeType === 1) {
            var attr = node.attributes
            // 解析属性
            for (var i=0; i<attr.length; i++) {
                if (attr[i].nodeName == 'v-model') {
                    var name = attr[i].nodeValue // 获取v-model绑定的属性名
                    node.addEventListener('input', function (e) {
                        vm[name] = e.target.value
                    })
                    new Watcher(vm, node, name, 'value')
                }
            }
        }
        // 节点类型为text
        if (node.nodeType === 3) {
            if (reg.test(node.nodeValue)) {
                var name = RegExp.$1 // 与正则表达式匹配的第一个字符串
                name = name.trim()
                new Watcher(vm, node, name, 'nodeValue')
            }
        }
    }
}

// 数据监听器
function Observer (obj, vm) {
    if (!obj || typeof(obj) !== 'object')  return
    Object.keys(obj).forEach(function (key) {
        defineReactive (vm, key, obj[key])
    })
}
function defineReactive (obj, key, val) {
    var dep = new Dep()
    Observer(val)  //监听子属性
    Object.defineProperty(obj, key, {
        get: function () {
            if (Dep.target) {
                dep.addSub(Dep.target)
            }
            return val
        },
        set: function (newVal) {
            if (val === newVal) return 
            val = newVal
            //作为发布者发布通知，触发订阅者的更新函数
            dep.notify()
        }
    })
}

// 消息订阅器，收集订阅者
function Dep () {
    this.subs = []
}
Dep.prototype = {
    addSub: function (sub) {
        this.subs.push(sub)
    },
    notify: function () {
        this.subs.forEach(function (sub) {
            sub.update()
        })
    }
}

// 订阅者
function Watcher (vm, node, name, type) {
    Dep.target = this
    this.name = name
    this.node = node
    this.vm = vm
    this.type = type
    this.update()
    Dep.target = null
}
Watcher.prototype = {
    update: function () {
        this.get()
        this.node[this.type] = this.value   // 订阅者执行相应的操作
    },
    // 获取data的属性值
    get: function () {
        this.value = this.vm[this.name]
    }
}

function Vue (options) {
    this.data = options.data
    var data = this.data
    Observer(data, this)
    var id = options.el
    var dom = new Compile(document.querySelector(id), this)
    // 编译完成后，将dom返回到app中
    document.querySelector(id).appendChild(dom)
}
var vm = new Vue({
    el: '#app',
    data: {
        text: 'hello world'
    }
})
</script>
</body>
</html>
```