![](https://upload-images.jianshu.io/upload_images/16527477-4902e59603843da5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 目录
- [目录](#目录)
- [概述](#概述)
- [实现思路](#实现思路)
  - [（1）version 1.0：](#1version-10)
  - [（2）version 2.0：类型检测（传入字符串或节点对象）](#2version-20类型检测传入字符串或节点对象)
  - [（3）version 3.0： 支持操作多个元素](#3version-30-支持操作多个元素)
  - [（4）version 4.0：优化代码](#4version-40优化代码)
- [总结](#总结)

## 概述

总所周知，jQuery是一个早期非常强大的js库，它小巧、快速、特性丰富，它在HTML的文档遍历、DOM操作，事件冒泡，动画以及AJAX使用方面提供了非常便捷易用的API。本文从学习的角度简要分析jQuery的封装机制。

## 实现思路

当给定需求如下：
```javascript
window.jQuery = ???
window.$ = jQuery

var $div = $('div')
$div.addClass('red')     // 可将所有 div 的 class 添加一个 red
$div.setText('hi')       // 可将所有 div 的 textContent 变为 hi
```
在只知道常用DOM API的情况下，为了模拟一个简易jQuery的封装机制，首先能想到的是在`Node`的原型上添加方法`addClass`和`setText`，但这样就破坏了DOM本身的封装，并不是一个好方法。更好的思路是设计一个新的函数来对原`node`添加装饰功能并返回。具体如下：

### （1）version 1.0：
```javascript
window.jQuery = function (node1) {
    var node = node1

    node.addClass = function (className) {
        node.classList.add(className)
    }

    node.setText = function (text) {
        node.textContent = text
    }

    return node
}
```

### （2）version 2.0：类型检测（传入字符串或节点对象）
```javascript
window.jQuery = function (node1) {
    var node

    if (typeof node1 === "string") {
        node = document.querySelector(node1)
    } else {
        node = node1
    }

    node.addClass = function (className) {
        node.classList.add(className)
    }

    node.setText = function (text) {
        node.textContent = text
    }

    return node
}

```

### （3）version 3.0： 支持操作多个元素

```javascript
window.jQuery = function (node1) {
    var node

    if (typeof node1 === "string") {
        node = document.querySelectorAll(node1)
    } else {
        node = {}
        node[0] = node
        node["length"] = 1
    }

    node.addClass = function (className) {
        for (var i = 0; i < node.length; i++) {
            node[i].classList.add(className)
        }
    }

    node.setText = function (text) {
        for (var i = 0; i < node.length; i++) {
            node[i].textContent = text
        }
    }

    return node
}
```

### （4）version 4.0：优化代码

```javascript
 window.jQuery = function (nodeOrSelector) {
    let nodes = {}

    // 返回一个纯净的数组
    if (typeof nodeOrSelector === "string") {
        let temp = document.querySelectorAll(nodeOrSelector)
        for (let i = 0; i < temp.length; i++) {
            nodes[i] = temp[i]
        }
        nodes.length = temp.length
    } else if (nodeOrSelector instanceof Node) {
        nodes = {
            0: nodeOrSelector,
            length: 1
        }

    }

    // 同时添加多个类(classes可以是一个字符串，也可以是一个数组集合了需要添加的类)
    nodes.addClass = function (classes) {

        if (typeof classes === "string") {
            for (let i = 0; i < nodes.length; i++) {
                nodes[i].classList.add(classes)
            }
        } else if (classes instanceof Array) {
            classes.forEach(value => {
                for (let i = 0; i < nodes.length; i++) {
                    nodes[i].classList.add(value)
                }
            })
        }
    }

    nodes.setText = function (text) {
        for (let i = 0; i < nodes.length; i++) {
            nodes[i].textContent = text
        }
    }

    return nodes
}

window.$ = window.jQuery
```
在4.0版里，我们能够得到
-  更纯净的`node`（原型链里不再有`Node.prototype`）
-  可同时添加多个类的`addClass`方法，接受的参数可以是一个字符串，即添加一个类，也可以是多个类组成的数组（因为错误处理我还没了解，所以其他类型抛出错误现在没有实现）
-  同时给得到的所有节点添加文字内容的`setText`方法

##  总结
以上的过程，旨在学习jQuery的封装机制。即jQuery是一个构造函数，它封装了原生js的属性和方法，以更于易用的api供我们使用。本文是对jQuery的一个简易版实现，理解了其中的原理，对于我们学习其他框架也是大有裨益的。

![加油！](https://upload-images.jianshu.io/upload_images/16527477-0a90ad0d1bea5d2f.gif?imageMogr2/auto-orient/strip)
