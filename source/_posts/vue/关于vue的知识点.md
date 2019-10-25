---
title: 关于vue的知识点
comments: true
date: 2019-10-24 15:39:46
categories:
 - vue
tags:
img:
---

## vue 是什么？

构建页面，为页面提供驱动，关注视图层。

它包含什么，有哪些功能？

* 生命周期

* 数据双向绑定

* 指令

* style和class动态绑定

* 全局API

* 内置组件

* 组件通信

* 路由

* 状态管理

* http请求工具axios

## 生命周期

* beforeCreate

实例组件刚创建，元素DOM和数据都还没有初始化，暂时不知道能在这个周期里面进行生命操作。

* created（指定'el'选项，指定template选项，将template编译到render函数中）

数据data已经初始化完成，方法也已经可以调用，但是DOM未渲染。在这个周期里面如果进行请求是可以改变数据并渲染，由于DOM未挂载，请求过多或者占用时间过长会导致页面线上空白。

* beforeMount（创建 vm.$el）

DOM未完成挂载，数据也初始化完成，但是数据的双向绑定还是显示双括号，这是因为Vue采用了Virtual DOM（虚拟Dom）技术。先占住了一个坑。

* mounted（挂载完毕）

数据和DOM都完成挂载，在上一个周期占位的数据把值给渲染进去。一般请求会放在这个地方，因为这边请求改变数据之后刚好能渲染。

* beforeUpdate(虚拟DOM重新渲染并应用更新)

只要是页面数据改变了都会触发，数据更新之前，页面数据还是原来的数据，当你请求赋值一个数据的时候会执行这个周期，如果没有数据改变不执行。

* updated

只要是页面数据改变了都会触发，数据更新完毕，页面的数据是更新完成的。beforeUpdate和updated要谨慎使用，因为页面更新数据的时候都会触发，在这里操作数据很影响性能和容易死循环。

* beforeDestroy(解除绑定，销毁子组件，以及事件监听器)

这个周期是在组件销毁之前执行，有点类似路由钩子beforeRouterLeave,都是在路由离开的时候执行，只不过beforeDestroy无法阻止路由跳转，但是可以做一些路由离开的时候操作

* destroyed(销毁完毕)

这个周期里面还可以使用data和method。比如一个倒计时组件，如果在路由跳转的时候没有清除，这个定时器还是在的，这时候就可以在这个里面清除计时器。

## 数据双向绑定

[实现数据双向绑定demo](https://wisteria-lynn.github.io/public/2019/10/25/vue/vue的数据双向绑定demo/)

## 指令

* __v-text__

主要用来更新textContent，等于js的text属性

```html
<!--两者等价-->
<span v-text="msg"></span>
<span>{{msg}}</span>
```

* __v-html__

它等同于JS的innerHtml属性，直接作为HTML进行渲染

* __v-pre__

v-pre主要用来跳过这个元素和它的子元素编译过程。可以用来显示原始的Mustache标签。跳过大量没有指令的节点加快编译。

* __v-cloak__

这个指令是用来保持在元素上直到关联实例结束时进行编译。

* __v-once__

v-once关联的实例，只会渲染一次。之后的重新渲染，实例极其所有的子节点将被视为静态内容跳过，这可以用于优化更新性能。

* __v-if | v-else | v-else-if__

v-if可以实现条件渲染

v-else是搭配v-if使用的，它必须紧跟在v-if或者v-else-if后面，否则不起作用。

v-else-if充当v-if的else-if块，可以链式的使用多次。可以更加方便的实现switch语句。

* __v-show__

也是用于根据条件展示元素。和v-if不同的是，如果v-if的值是false，则这个元素被销毁，不在dom中。但是v-show的元素会始终被渲染并保存在dom中，它只是简单的切换css的dispaly属性。

>v-if 是真正的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建；也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
 v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 的 “display” 属性进行切换。
 所以，v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景；v-show 则适用于需要非常频繁切换条件的场景。
 

* __v-for__

用v-for指令根据遍历数组来进行渲染
有下面两种遍历形式

```html
<div v-for="(item,index) in items"></div>   //使用in，index是一个可选参数，表示当前项的索引
<div v-for="item of items"></div>   //使用of
```

下面是一个例子，并且在v-for中，拥有对父作用域属性的完全访问权限。

```html
<ul id="app">
    <li v-for="item in items">
        {{parent}}-{{item.text}}
    </li>
</ul>
<script type="text/javascript">
    var example = new Vue({
      el:'#app',
      data:{
        parent:'父作用域'
        items:[
          {text:'文本1'},
          {text:'文本2'}
        ]
      }
    })
</script>
```

会被渲染为：

```html
<ul id="app">
    <li>父作用域-文本1</li>
    <li>父作用域-文本2</li>
</ul>
```

>注意：当v-for和v-if同处于一个节点时，v-for的优先级比v-if更高。这意味着v-if将运行在每个v-for循环中

__key的作用__

vue的dom渲染是虚拟dom，数据发生变化时，diff算法会只比较更改的部分，如果数据项的顺序被改变，Vue将不是移动DOM元素来匹配数据项的改变，而是简单复用此处每个元素，
并且确保它在特定索引下显示已被渲染过的每个元素。举例说明：有一个列表我们现在在中间插入了一个元素，diff算法会默认复用之前的列表并在最后追加一个，如果列表存在选中
一类的状态则会随着复用出现绑定错误的情况而不是跟着原元素，key的作用就可以给他一个标识，让状态跟着数据渲染。（这一块是我自己的一个大概理解，表述不太清楚，具体的可以
去查一下文档，本文就不具体描述此问题了。）

* __v-bind__

v-bind用来动态的绑定一个或者多个特性。没有参数时，可以绑定到一个包含键值对的对象。常用于动态绑定class和style。以及href等。
简写为一个冒号【 ：】


* __v-model__

这个指令用于在表单上创建双向数据绑定。
v-model会忽略所有表单元素的value、checked、selected特性的初始值。因为它选择Vue实例数据做为具体的值。

__v-model修饰符__

<1> .lazy

默认情况下，v-model同步输入框的值和数据。可以通过这个修饰符，转变为在change事件再同步。

```html
<input v-model.lazy="msg">
```

<2> .number
自动将用户的输入值转化为数值类型

<3> .trim
自动过滤用户输入的首尾空格

* __v-on__

v-on主要用来监听dom事件，以便执行一些代码块。表达式可以是一个方法名。
简写为：【 @ 】

__事件修饰符__

.stop 阻止事件继续传播

.prevent 事件不再重载页面

.capture 使用事件捕获模式,即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理

.self 只当在 event.target 是当前元素自身时触发处理函数

.once 事件将只会触发一次

.passive 告诉浏览器你不想阻止事件的默认行为

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成  -->
<!-- 这其中包含 `event.preventDefault()` 的情况 -->
<div v-on:scroll.passive="onScroll">...</div>
```

>使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用v-on:click.prevent.self会阻止所有的点击，而 v-on:click.self.prevent 只会阻止对元素自身的点击。

## 属性-computed,watch.....

* __computed__

getter和setter

```vuejs
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

* __watch__

监听对象

```vuejs
watch: {
    'obj.question': function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
}
```

深度监听

```vuejs
watch: {
    obj: {
        handler: function (newQuestion, oldQuestion) {
          this.answer = 'Waiting for you to stop typing...'
          this.debouncedGetAnswer()
        },
        deep: true,
        immediate: true
    }
}
```

>immediate的作用：当值第一次进行绑定的时候并不会触发watch监听，使用immediate则可以在最初绑定的时候执行。

## style和class动态绑定

Class 可以通过对象语法和数组语法进行动态绑定：

对象语法：

```html
<div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>

data: {
  isActive: true,
  hasError: false
}
```

数组语法：

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Style 也可以通过对象语法和数组语法进行动态绑定：

对象语法：

```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

data: {
  activeColor: 'red',
  fontSize: 30
}
```

数组语法：

```html
<div v-bind:style="[styleColor, styleSize]"></div>

data: {
  styleColor: {
     color: 'red'
   },
  styleSize:{
     fontSize:'23px'
  }
}
```

## 全局API

* __$nextTick__

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

解决的问题：有些时候在改变数据后立即要对dom进行操作，此时获取到的dom仍是获取到的是数据刷新前的dom，无法满足需要，这个时候就用到了$nextTick。

* __$set__

向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新属性，因为 Vue 无法探测普通的新增属性 

## 内置组件

* __component__

有两个属性:is|inline-template

渲染一个‘元组件’为动态组件，按照'is'特性的值来渲染成那个组件

* __transition__

为组件的载入和切换提供动画效果，具有非常强的可定制性

* __transition-group__

作为多个元素/组件的过渡效果

* __keep-alive__

包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们

* __slot__

作为组件模板之中的内容分发插槽，slot元素自身将被替换

## 组件通信

* __props__

1.父子组件通信
2.单向数据流
3.以数组的形式接受参数
4.或以对象的方式接受参数，可以约定接受数据的类型。
5.子组件想修改时，只能通过$emit派发自定义事件，父组件收到后进行修改。

* __ref 与 $parent / $children__

1.适用 父子组件通信
2.ref：如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例
3.$parent / $children：访问父 / 子实例

* __provide / inject 适用于 隔代组件通信__

* __vuex__

1.父子组件/非父子组件/隔代/兄弟

* __Bus__

1.父子组件/非父子组件/隔代/兄弟
2.eventBus的原理是引入一个新的vue实例，然后通过分别调用这个实例的事件触发和监听来实现通信和参数传递。

```js
import Vue from 'vue';  
export default new Vue();  

// 
import eventBus from 'eventBus.js'; 
// 接收
eventBus .$on('getTarget', this.getTarget); 
// 触发 
eventBus.$emit("getTarget", 22);
```

## 路由

vue-router 有 3 种路由模式：hash、history、abstract，对应的源码如下所示：

* hash:  使用 URL hash 值来作路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器；


* history :  依赖 HTML5 History API 和服务器配置。具体可以查看 HTML5 History 模式；


* abstract :  支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式.

__能说下 vue-router 中常用的 hash 和 history 路由模式实现原理吗？__

__（1）hash 模式的实现原理__

早期的前端路由的实现就是基于 location.hash 来实现的。其实现原理很简单，location.hash 的值就是 URL 中 # 后面的内容。比如下面这个网站，它的 location.hash 的值为 '#search'：

`https://www.word.com#search`

hash  路由模式的实现主要是基于下面几个特性：

* URL 中 hash 值只是客户端的一种状态，也就是说当向服务器端发出请求时，hash 部分不会被发送；
* hash 值的改变，都会在浏览器的访问历史中增加一个记录。因此我们能通过浏览器的回退、前进按钮控制hash 的切换；
* 可以通过 a 标签，并设置 href 属性，当用户点击这个标签后，URL 的 hash 值会发生改变；或者使用  JavaScript 来对 loaction.hash 进行赋值，改变 URL 的 hash 值；
* 我们可以使用 hashchange 事件来监听 hash 值的变化，从而对页面进行跳转（渲染）。

__（2）history 模式的实现原理__

HTML5 提供了 History API 来实现 URL 的变化。其中做最主要的 API 有以下两个：history.pushState() 和 history.repalceState()。这两个 API 可以在不进行刷新的情况下，操作浏览器的历史纪录。唯一不同的是，前者是新增一个历史记录，后者是直接替换当前的历史记录，如下所示：

```js
window.history.pushState(null, null, path);
window.history.replaceState(null, null, path);
```

history 路由模式的实现主要基于存在下面几个特性：

* pushState 和 repalceState 两个 API 来操作实现 URL 的变化 ；
* 我们可以使用 popstate  事件来监听 url 的变化，从而对页面进行跳转（渲染）；
* history.pushState() 或 history.replaceState() 不会触发 popstate 事件，这时我们需要手动触发页面跳转（渲染）。

## 状态管理

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。每一个 Vuex 应用的核心就是 store（仓库）。“store” 基本上就是一个容器，它包含着你的应用中大部分的状态 ( state )。
（1）Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
（2）改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化。
主要包括以下几个模块：

State：定义了应用状态的数据结构，可以在这里设置默认的初始状态。
Getter：允许组件从 Store 中获取数据，mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性。
Mutation：是唯一更改 store 中状态的方法，且必须是同步函数。
Action：用于提交 mutation，而不是直接变更状态，可以包含任意异步操作。
Module：允许将单一的 Store 拆分为多个 store 且同时保存在单一的状态树中。

## 插槽

## http请求工具axios