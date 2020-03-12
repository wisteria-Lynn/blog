---
title: vue锚点滑动
comments: true
date: 2020-03-12 14:28:51
categories:
tags: - vue
img:
---

```js
/**
 * 平滑滑动
 * total 目标元素距离顶部高度
 * distance  滚动条距离顶部高度
 * element 包含滚动条的div元素id | 值为空时，元素为document
 * */
export const Scroll = {
	element: '',
	scrollInt: function (total, distance, element) {
		this.element = element
		let step = Math.abs(distance - total) / 30
		if (total > distance) {
			Scroll.scrollDown(step, total, distance)
		} else {
			Scroll.scrollUp(step, total, distance)
		}
	},
	//向下滑动
	scrollDown: function (step, total, distance) {
		if (distance < total) {
			distance += step
			if (distance > total) {
				distance = total
			}
			if(this.element === ''){
				document.documentElement.scrollTop = distance
			} else {
				document.getElementById(this.element).scrollTop = distance
			}
			setTimeout(() => Scroll.scrollDown(step, total, distance), 20)
		}
	},
	//向上滑动
	scrollUp: function (step, total, distance) {
		if (distance > total) {
			distance -= step
			if (distance < total) {
				distance = total
			}
			if(this.element === ''){
				document.documentElement.scrollTop = distance
			} else {
				document.getElementById(this.element).scrollTop = distance
			}
			setTimeout(() => Scroll.scrollUp(step, total, distance), 20)
		}
	},
	// 路由切换，滚动条回顶部
	goTop: function (router) {
		router.afterEach((to, from, next) => {
			window.scrollTo(0, 0)
		})
	},
	// 获取滚动条距离顶部高度
	scrollTop:function(){
		let scroll_top = 0
		if (document.documentElement && document.documentElement.scrollTop) {
			scroll_top = document.documentElement.scrollTop
		} else if (document.body) {
			scroll_top = document.body.scrollTop
		}
		return scroll_top
	}
}

```


```js
export default{
    methods: {
    		// 锚点，平滑滑动
    		goAnchor(element) {
    			let total = document.getElementById(element).offsetTop; //目标元素距离顶部高度
    			Scroll.scrollInt(total - 40, Scroll.scrollTop(), "");
    			this.curTab = element;
    		}
    	},
    mounted() {
    		this.$nextTick(() => {
    			this.curTab = Object.keys(this.pageNav)[0];
    			// 监听滚动条 节流
    			this.throttled = throttle(() => {
    				this.isScroll = Scroll.scrollTop() > this.distance;
    				if (this.isScroll) {
    					for (let i = 0; i < this.list.length; i++) {
    						let element = document.getElementById(this.list[i]);
    						if (
    							element.offsetTop >= Scroll.scrollTop() &&
    							element.offsetTop <
    								Scroll.scrollTop() + element.offsetHeight
    						) {
    							this.curTab = this.list[i];
    						}
    					}
    				} else {
    					this.curTab = Object.keys(this.pageNav)[0];
    				}
    			}, 100);
    			window.addEventListener("scroll", this.throttled, true);
    		});
    	},
    	destroyed() {
    		window.removeEventListener("scroll", this.throttled, true);
    	}
}
```

```js
/**
 * 节流
 */
export const throttle = function (fn, delay) {
	let timer;
	return function () {
		if(!timer){
			timer = setTimeout(function () {
				timer = null
				fn.call(this)
			}, delay)
		}
	}
}
```

```js
/**
 * 按钮 点击节流
 */
export const stopClick = function (name,callBack,time=1000,msg=`请等待1s后再次点击`){
	return new Promise((resolve,reject)=>{
		if(name){
			console.log(`${msg} >>>>>>>>>`)
			return false
		}
		callBack(true)
		setTimeout(()=>{
			callBack(false)
		},time)
		resolve()
	})
}
```