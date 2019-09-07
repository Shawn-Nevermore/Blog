![](https://upload-images.jianshu.io/upload_images/16527477-3a2fb7b5e92df2ab.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#  目录
- [目录](#目录)
- [概述](#概述)
- [EventTarget接口](#eventtarget接口)
  - [EventTarget.addEventListener()](#eventtargetaddeventlistener)
  - [EventTarget.removeEventListener()](#eventtargetremoveeventlistener)
  - [EventTarget.dispatchEvent()](#eventtargetdispatchevent)
- [事件模型](#事件模型)
  - [绑定监听函数](#绑定监听函数)
      - [（1）HTML 的 on- 属性](#1html-的-on--属性)
      - [（2）DOM level 0：元素节点的事件属性](#2dom-level-0元素节点的事件属性)
      - [（3）DOM level 2：EventTarget.addEventListener()](#3dom-level-2eventtargetaddeventlistener)
  - [this指向](#this指向)
  - [事件传播（propagation）](#事件传播propagation)
      - [一个例子](#一个例子)
  - [事件代理/委托（delegation）](#事件代理委托delegation)
- [Event 对象](#event-对象)
  - [实例属性](#实例属性)
      - [Event.bubbles，Event.eventPhase](#eventbubbleseventeventphase)
      - [Event.cancelable，Event.cancelBubble，event.defaultPrevented](#eventcancelableeventcancelbubbleeventdefaultprevented)
      - [Event.currentTarget，Event.target](#eventcurrenttargeteventtarget)
      - [Event.isTrusted](#eventistrusted)
  - [实例方法](#实例方法)
      - [Event.preventDefault()](#eventpreventdefault)
      - [Event.stopPropagation() 和 Event.stopImmediatePropagation()](#eventstoppropagation-和-eventstopimmediatepropagation)
      - [Event.composedPath()](#eventcomposedpath)
- [总结](#总结)

#  概述
DOM 的事件操作（监听和触发），都定义在`EventTarget`接口。所有节点对象以及一些需要事件通信的浏览器内置对象（比如，`XMLHttpRequest`、`AudioNode`、`AudioContext`）都部署了这个接口。

该接口主要提供三个实例方法：
-  `addEventListener`：绑定事件的监听函数
-  `removeEventListener`：移除事件的监听函数
-  `dispatchEvent`：触发事件


#  EventTarget接口  
##  EventTarget.addEventListener()
> EventTarget.addEventListener() 用于在当前节点或对象上，定义一个指定事件的监听函数。一旦这个事件发生，就会执行监听函数。该方法没有返回值。

```javascript
target.addEventListener(type, listener[, useCapture])
```

其中，
-  `type`：事件名称，大小写敏感
-  `listener`：监听函数（也可以是一个具有`handleEvent`方法的对象）。事件发生时，会调用该监听函数
-  `useCapture`：布尔值（还可以是一个[属性配置对象](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)
），表示监听函数是否在捕获阶段触发，默认为`false`（监听函数只在冒泡阶段被触发）

同时添加多个不同的监听函数时，按添加的先后顺序执行，且不重复执行。

监听函数内部的`this`，指向当前事件所在的那个对象。
```javascript
// HTML 代码如下
// <p id="para">Hello</p>

var para = document.getElementById('para')
para.addEventListener('click', function (e) {
  console.log(this.nodeName); // "P"
}, false)
```
上面代码中，监听函数内部的`this`指向事件所在的对象`para`。

##  EventTarget.removeEventListener()
> EventTarget.removeEventListener 方法用来移除 addEventListener 方法添加的事件监听函数。该方法没有返回值。

需满足如下条件：
-  必须在同一个元素节点，即`target`一致
-  必须`addEventListener`方法添加的那个监听函数，即`listener`一致（匿名函数无效）
-  `useCapture`一致

##  EventTarget.dispatchEvent()
> EventTarget.dispatchEvent 方法在当前节点上触发指定事件，从而触发监听函数的执行。

该方法返回一个布尔值，只要有一个监听函数调用了 `Event.preventDefault()`，则返回值为 `false`，否则为 `true`。

根据 `dispatchEvent` 方法的返回值，判断事件是否被取消了。
```javascript
var canceled = !cb.dispatchEvent(event);
if (canceled) {
  console.log('事件取消');
} else {
  console.log('事件未取消');
}
```
#  事件模型
##  绑定监听函数
####  （1）HTML 的 on- 属性
```html 
<body onload="doSomething()">
<div onclick="console.log('触发事件')">
```


```html
<div onClick="console.log(2)">
  <button onClick="console.log(1)">点击</button>
</div>
```
由于`on-`属性的监听代码，只在**冒泡阶段触发**，上面的代码会先输出`1`，再输出`2`。


**注意**：这些属性的值是将会执行的代码，而不是一个函数。即等同于执行`eval(doSomething())`。如下图：

![](https://upload-images.jianshu.io/upload_images/16527477-08f955fa421dcc60.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



这种方法违反了 HTML 与 JavaScript 代码相分离的原则，将两者写在一起，不利于代码分工，因此不推荐使用。

#### （2）DOM level 0：元素节点的事件属性
```javascript
window.onload = doSomething

div.onclick = function (event) {
  console.log('触发事件');
}
```
使用这个方法指定的监听函数，也是只会在**冒泡阶段触发**。

这种方法与 HTML 的`on-`属性的差异是，它的值是函数名（`doSomething`），而不像后者，必须给出完整的监听代码（`doSomething()`）。

缺点：同一个事件只能定义一个监听函数。如果定义两次`onclick`属性，后一次定义会覆盖前一次。因此，也不推荐使用。

#### （3）DOM level 2：EventTarget.addEventListener()
定义见上面。

优点：
-  相当于一个队列，同一个事件可以添加多个监听函数。
-  能够指定在哪个阶段（捕获阶段还是冒泡阶段）触发监听函数。
-  除了 DOM 节点，其他对象（比如`window`、`XMLHttpRequest`等）也有这个接口，它等于是整个 JavaScript 统一的监听函数接口。

##  this指向
>监听函数内部的 `this` 指向触发事件的那个元素节点。
```javascript
// HTML 代码如下
// <button id="btn">点击</button>
var btn = document.getElementById('btn')

// 写法一
btn.onclick = function () {
  console.log(this.id)
}

// 写法二
btn.addEventListener('click', function (e) {
    console.log(this.id)
  }, false)
```
上面两种写法，点击按钮以后也是输出`btn`。

##  事件传播（propagation）
一个事件发生后，会在子元素和父元素之间传播，分成三个阶段：
1.  从`window`对象传导到目标节点（上层传到底层），称为“捕获阶段”（capture phase）
2.  在目标节点上触发，称为“目标阶段”（target phase）
3.  从目标节点传导回`window`对象（从底层传回上层），称为“冒泡阶段”（bubbling phase）

![](https://upload-images.jianshu.io/upload_images/16527477-8a2d9fbc32947c05.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####  [一个例子](http://js.jirengu.com/zayipuvafo/1/edit?html,js,output "帮助理解propagation")

点击中心如图（捕获阶段结束时）：

![](https://upload-images.jianshu.io/upload_images/16527477-50ca433aafba33a7.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其js部分如下：
```javascript
let divs = $('div').get()
let n = 0

//  捕获阶段
for (let i = 0; i < divs.length; i++) {
  divs[i].addEventListener('click', (e) => {
    console.log(e.eventPhase)    // 打印出当前节点所处的阶段
    setTimeout(() => {
      divs[i].classList.add('active')
    }, n * 500)
    n ++
  }, true)
}

// 冒泡阶段
for (let i = 0; i < divs.length; i++) {
  divs[i].addEventListener('click', (e) => {
    console.log(e.eventPhase)
    setTimeout(() => {
      divs[i].classList.remove('active')
    }, n * 500)
    n ++
  })
}
```
如果点击中心圆圈，在捕获阶段，因为由上层到底层的`div`会依次触发`click`事件，进而依次给这些`div`添加`active`类，即填充背景颜色。视觉效果上，就是呈波纹状地向内染色。
相对应地，在冒泡阶段，是由内而外地依次给`div`移除`active`类，视觉效果上，是呈波纹状地褪色。
以上的动画效果只需要一次点击，因为在最里层的div同时处于捕获和冒泡阶段，在`console.log(e.eventPhase)`执行后，显示为`target`阶段：
![](https://upload-images.jianshu.io/upload_images/16527477-428b54ab0b45b08e.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图，最里层的被点击的`div`打印了两次`2`，其余的`div`在捕获阶段均打印`1`，在冒泡阶段均打印`3`。符合 [eventPhase](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/eventPhase#%E4%BA%8B%E4%BB%B6%E9%98%B6%E6%AE%B5%E5%B8%B8%E9%87%8F) 的定义。

##  事件代理/委托（delegation）
> 由于事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这种方法叫做事件的代理（委托）。

优点：

-  只要定义一个监听函数，就能处理多个子节点的事件，而不用在每个子节点上定义监听函数。（减少内存消耗）
-  而且以后再添加子节点，监听函数依然有效。（动态绑定事件）

具体实践需要项目经验，此处先参考：[JavaScript 事件委托详解
](https://zhuanlan.zhihu.com/p/26536815)


#  Event 对象
事件对象 `Event` 是监听函数的参数，本身也是一个构造函数，继承自 `Event.prototype` 对象。
```javascript
event = new Event(type, options)
```
其中，
1)  `type` 是字符串，表示事件的名称

2)  `options` 是一个对象，表示事件对象的配置。该对象主要有下面两个属性
    -  `bubbles`：布尔值，可选，默认为`false`，表示事件对象是否冒泡
    -  `cancelable`：布尔值，可选，默认为`false`，表示事件是否可以被取消，即能否用`Event.preventDefault()`取消这个事件

##  实例属性
####  Event.bubbles，Event.eventPhase
1.  `Event.bubbles` 属性返回一个布尔值，表示当前事件是否会冒泡。该属性只读

2.  `Event.eventPhase` 属性返回一个**整数常量**，表示事件目前所处的阶段。该属性只读
    -  0，事件目前没有发生
    -  1，事件目前处于捕获阶段，即处于从祖先节点向目标节点的传播过程中
    -  2，事件到达目标节点，即 `Event.target` 属性指向的那个节点
    -  3，事件处于冒泡阶段，即处于从目标节点向祖先节点的反向传播过程中

####  Event.cancelable，Event.cancelBubble，event.defaultPrevented
1. `Event.cancelable` 属性返回一个布尔值，表示事件是否可以取消。该属性为只读属性。如果为`true`，则可以调用 `Event.preventDefault()` 方法来取消事件；如果为 `false`，则调用 `Event.preventDefault()` 方法无效。因此，需要先用这个布尔值来判断，配合使用

2.  `Event.cancelBubble` 属性是一个布尔值，如果设为 `true`，相当于执行`Event.stopPropagation()`，可以阻止事件的传播

3.  `Event.defaultPrevented` 属性返回一个布尔值，表示该事件是否调用过`Event.preventDefault` 方法。该属性只读

####  Event.currentTarget，Event.target
1.  `Event.currentTarget` 属性返回事件当前所在的节点，即正在执行的监听函数所绑定的那个节点
2.  `Event.target` 属性返回原始触发事件的那个节点，即事件最初发生的节点。

事件传播过程中，不同节点的监听函数内部的 `Event.target` 与 `Event.currentTarget` 属性的值是不一样的，前者总是不变的，后者则是指向监听函数所在的那个节点对象。

来看[一个例子](http://js.jirengu.com/canap/1/edit?html,js,output)，js部分代码如下：
```javascript
let btn = document.getElementById('btn')

document.addEventListener('click',function(e){
  console.log(e.target)
  console.log(e.currentTarget)
  console.log(this)
})

btn.addEventListener('click',function(e){
  console.log(e.target)
  console.log(e.currentTarget)
  console.log(this)
})
```

结果如下：

![](https://upload-images.jianshu.io/upload_images/16527477-ed3b6a5a62516e0b.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一般来说，`Event.target` 是事件的触发者，`Event.currentTarget` 是事件的监听者，`this` 的值始终等于 `currentTarget` 的值。

####  Event.isTrusted
> Event.isTrusted 属性返回一个布尔值，表示该事件是否由真实的用户行为产生。

比如，用户点击链接会产生一个 `click` 事件，该事件是用户产生的；`Event` 构造函数生成的事件，则是脚本产生的。
```javascript
var evt = new Event('foo')
evt.isTrusted // false
```
##  实例方法
####  Event.preventDefault()
> Event.preventDefault 方法取消浏览器对当前事件的默认行为。

**注意**：该方法只是取消事件对当前元素的默认影响，不会阻止事件的传播。如果要阻止传播，可以使用 `stopPropagation()` 或 `stopImmediatePropagation()` 方法。

####  Event.stopPropagation() 和 Event.stopImmediatePropagation()
1. `Event.stopPropagation()`方法可以阻止事件的传播，但并不会取消该事件。
2. `Event.stopImmediatePropagation()` 方法则可以彻底取消该事件，不再触发后面所有该事件的监听函数。
```javascript
p.addEventListener('click', function (event) {
  event.stopPropagation()
  //  event.stopImmediatePropagation()
  console.log(1)
})

p.addEventListener('click', function(event) {
  console.log(2)  // stopPropagation会触发，stopImmediatePropagation不会触发
})
```
上面代码中，`p`元素绑定了两个`click`事件的监听函数。
-  `stopPropagation`方法只能阻止这个事件的传播，不能取消这个事件，因此，第二个监听函数会触发。输出结果会先是`1`，然后是`2`
-  `stopImmediatePropagation` 方法可以彻底取消这个事件，使得后面绑定的所有`click`监听函数都不再触发。所以，只会输出`1`，不会输出`2`

#### Event.composedPath() 
>  Event.composedPath() 返回一个数组，成员是事件的最底层节点和依次冒泡经过的所有上层节点。

```html
<div>
  <p>Hello</p>
</div>
```
```javascript
var div = document.querySelector('div');
var p = document.querySelector('p');

div.addEventListener('click', function (e) {
  console.log(e.composedPath());
}, false);
// [p, div, body, html, document, Window]
```

上面代码中，`click`事件的最底层节点是`p`，向上依次是`div`、`body`、`html`、`document`、`Window`。

#  总结
本文是对DOM事件的学习总结，主要是内容为事件传播过程的捕获和冒泡，以及事件本身的常见属性和方法。并不涉及具体的API，比如鼠标事件、键盘事件等的API。参考：[阮一峰教程](https://wangdoc.com/javascript/events/index.html)

![加油！](https://upload-images.jianshu.io/upload_images/16527477-522291d5059b7339.gif?imageMogr2/auto-orient/strip)
