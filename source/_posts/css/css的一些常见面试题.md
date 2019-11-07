---
title: css的一些常见面试题
comments: true
date: 2019-11-06 15:19:18
categories:
 - css
tags:
img:
---

## flex布局

flex:Flexible Box(弹性布局)

为盒状模型提供最大的灵活性，任何一个容器都可以指定为Flex布局。

> Webkit内核的浏览器，必须加上-webkit前缀。`/*safari*/`

```html
<div class="box">
 <div>1</div>
 <div>2</div>
 <div>3</div>
</div>
```
```css
.box{
  display:flex;
}
```

* 在父元素设置，水平主轴，垂直交叉轴

  * flex-direction:row|row-reverse|column|column-reverse(决定主轴方向：水平左端|水平右端|垂直上沿|垂直下沿)
  
  * flex-wrap:nowrap|wrap|wrap-reverse;(轴线排不下，如何换行:默认不换行|换行，第一行在上方|换行，第一行在下方)
  
  * flex-flow:row nowrap;(是前两者的简写)
  
  * justify-content:flex-start|flex-end|center|space-between|space-around;(主轴对齐方式：左对齐|右对齐|居中|两端对齐|间隔对齐)
  
  * align-item:flex-start|flex-end|center|baseline|stretch;(交叉轴如何对齐：上对齐|下对齐|居中|第一行文字的基线对齐|默认沾满容器)
  
  * align-content:flex-start|flex-end|center|space-between|space-around|stretch;(多跟轴线的对齐方式，只有一根轴线，属性无效。)
  
* 子元素设置

  * order:0;(子元素排列顺序。数值越小，排列越靠前)
  
  * flex-grow:0;(子元素放大比例)
  
  * flex-shrink:0;(子元素缩小比例)
  
* 子元素的float,clear,vertical-align属性失效

## css3新特性

* __transtion 过渡效果。__

 * transition-property:none | all | `[ <IDENT> ]`;(指定元素的执行属性:无|所有属性|指定元素属性)
 
 * transition-duration ： time;(默认为0，元素转换过程的持续时间,单位为s或ms) 
 
 * transition-timing-function:ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier;(根据时间的推进去改变属性值的变换速率:逐渐变慢，默认|均速|加速|减速|加速然后减速|自定义特制的贝赛尔曲线)
 
 * transition-delay:time;(一个动画延迟执行的时间：默认为0) 

```css
/*多个属性过度，逗号隔开*/
.box{
  transition:background 0.5s ease-in,color 0.3s ease-out;
}
```

transition-property可指定的元素属性如下：

```text
1、color: 通过红、绿、蓝和透明度组件变换（每个数值处理）如：background-color,border-color,color,outline-color等css属性；

2、length: 真实的数字 如：word-spacing,width,vertical-align,top,right,bottom,left,padding,outline-width,margin,min-width,min-height,max-width,max-height,line-height,height,border-width,border-spacing,background-position等属性；

3、percentage:真实的数字 如：word-spacing,width,vertical-align,top,right,bottom,left,min-width,min-height,max-width,max-height,line-height,height,background-position等属性；

4、integer离散步骤（整个数字），在真实的数字空间，以及使用floor()转换为整数时发生 如：outline-offset,z-index等属性；

5、number真实的（浮点型）数值，如：zoom,opacity,font-weight,等属性；

6、transform list:详情请参阅：《CSS3 Transform》

7、rectangle:通过x, y, width 和 height（转为数值）变换，如：crop

8、visibility: 离散步骤，在0到1数字范围之内，0表示“隐藏”，1表示完全“显示”,如：visibility

9、shadow: 作用于color, x, y 和 blur（模糊）属性,如：text-shadow

10、gradient: 通过每次停止时的位置和颜色进行变化。它们必须有相同的类型（放射状的或是线性的）和相同的停止数值以便执行动画,如：background-image

11、paint server (SVG): 只支持下面的情况：从gradient到gradient以及color到color，然后工作与上面类似

12、space-separated list of above:如果列表有相同的项目数值，则列表每一项按照上面的规则进行变化，否则无变化

13、a shorthand property: 如果缩写的所有部分都可以实现动画，则会像所有单个属性变化一样变化
```

* __ainimation实现动画效果__

* __translate 3D建模效果__

## img中alt和title的区别

* 图片中的 alt属性是在图片不能正常显示时出现的文本提示。alt有利于SEO优化

* 图片中的 title属性是在鼠标在移动到元素上的文本提示。

## css创建三角形

```css
div{
     width: 0;
     height: 0;
     border-top: 40px solid transparent;
     border-left: 40px solid transparent;
     border-right: 40px solid transparent;
     border-bottom: 40px solid #ff0000;
}
```

原理：css的border边框是对线分割，当元素有高宽时是梯形，没有高宽时是三角形。

## css的盒子模型

* 标准盒子：width = content + border +padding

* IE怪异盒模型：width = content

## div水平居中

* `margin:auto;`

* `position:absolute;translate(-50%,0)`

* div增加一个父元素，父元素使用`display:flex;justify-content:center;`

## div垂直水平居中

* 使用`position,translate`属性。

* div增加一个父元素，父元素使用`display:flex;justify-content:center;align-item:center;`

## 图片垂直居中

* 增加一个父元素，父元素使用`display:flex;align-item:center;`

* 增加一个祖父元素`display:table`，增加一个父元素`display:table-cell;vertical-align:middle;`

## 清除浮动

浮动的影响：脱离文档流，造成父元素的塌陷。

* clear:both;在浮动元素下方添加空div,并给该元素写css样式 `{clear:both;height:0;overflow:hidden;}`

* after伪类
```css
float_div:after{
content:".";
clear:both;
display:block;
height:0;
overflow:hidden;
visibility:hidden;
}
.float_div{
 zoom:1
}
```

## 三栏布局，左右固定，中间自适应

> 圣杯布局/双飞翼布局

```html
<head>
<style>
        * {
            margin: 0;
            padding: 0;
        }
        .middle,
        .left,
        .right {
            position: relative;
            float: left;
            min-height: 130px;
        }
        .container {
            padding: 0 220px 0 200px;
            overflow: hidden;
        }
        .left {
            margin-left: -100%;
            left: -200px;
            width: 200px;
            background: red;
        }
        .right {
            margin-left: -220px;
            right: -220px;
            width: 220px;
            background: green;
        }
        .middle {
            width: 100%;
            background: blue;
            word-break: break-all;
        }
    </style>
</head>
<body>
    <div class='container'>
        <div class='middle'></div>
        <div class='left'></div>
        <div class='right'></div>
    </div>
</body>
```

## display:none 和 visibility: hidden的区别

* display:none 隐藏对应的元素，在文档布局中不再给它分配空间，它各边的元素会合拢，就当他从来不存在。

* visibility:hidden 隐藏对应的元素，但是在文档布局中仍保留原来的空间。

## CSS中 link 和@import 的区别是？

* link属于HTML标签，而@import是CSS提供的

* 页面被加载的时，link会同时被加载，而@import引用的CSS会等到页面被加载完再加载

* import只在IE5以上才能识别，而link是HTML标签，无兼容问题

* 渲染：!import > 内联 > style标签 > @import > link