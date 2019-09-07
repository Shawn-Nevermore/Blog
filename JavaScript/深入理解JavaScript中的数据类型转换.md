![](https://upload-images.jianshu.io/upload_images/16527477-475bac4182bd7ef2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 目录
- [目录](#目录)
- [概述](#概述)
- [抽象值操作（Abstract Operations）](#抽象值操作abstract-operations)
  - [1. ToString](#1-tostring)
  - [2. ToNumber](#2-tonumber)
  - [3. ToBoolean](#3-toboolean)
- [显示强制类型转换](#显示强制类型转换)
  - [toString()](#tostring)
  - [一元运算符（+ -）](#一元运算符--)
  - [parseInt()和parseFloat()](#parseint和parsefloat)
  - [!!](#)
- [隐式强制类型转换](#隐式强制类型转换)
  - [二元运算符（+）](#二元运算符)
  - [隐式转换为布尔值](#隐式转换为布尔值)
- [转换为Object](#转换为object)
- [总结](#总结)


#  概述
开篇先来一段神代码：
```javascript
(!(~+[])+{})[--[~+""][+[]]*[~+[]]+~~!+[]]+({}+[])[[~!+[]*~+[]]] = sb
```
这里面牵扯的东西很多，容我一一道来。

一般来说，类型转换发生在静态类型语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时。然而在JavaScript中通常将它们统称为强制类型转换。
在学习过程中，我认为更确切的是“显式强制类型转换”和“隐式强制类型转换”。而关于显隐的界限，我更倾向于《你不知道的JavaScript》中的说法，即它们是相对的，取决于你的理解，比如如果你明白 `a + ""` 的原理，那么它对于你来说就是显式的。

Js中有7种数据类型，本文主要涉及到的是转化为Number，String，Boolean的规则和方法，顺带提一下转化为Object。在这之前，先了解下类型转换的底层原理。

#  抽象值操作（Abstract Operations）
ToPrimitive(input [, PreferredType]) ，由[7.1.1](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-toprimitive)定义可知，它是包含两个参数的抽象操作，一个是 input ，一个是可选参数 PreferredType。该操作就是把参数input转化为原始数据类型。如果input可以同时转化为多个原始数据，那么会优先参考PreferredType的值。

转换的原则：
1.  input为原始数据类型：返回input自身
2.  input为对象：在没有改写或自定义toPrimitive方法的条件下，默认为Number（Date默认String）。PreferredType是String，则先调用toString()，结果不是原始值的话再调用valueOf()，还不是原始值的话则抛出TypeError错误；PreferredType是Number，则先调用valueOf()再调用toString()。

##  1. ToString
[ToString](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-tostring)负责处理非字符串到字符串的强制类型转换。规则如下图所示：
![](https://upload-images.jianshu.io/upload_images/16527477-401b56347041299c.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

number的转换规则参考[7.1.12.1](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-tostring-applied-to-the-number-type) 

对普通对象来说，内部调用ToPrimitive方法实现，除非自行定义，否则用Object.prototype.toString()返回内部属性[[Class]]的值，如`"[object Object]"`。

数组的默认toString()方法经过了重新定义，将所有单元字符串化以后再用`","`连接起来：
```javascript
var a = [1, 2, 3]
a.toString()      // "1,2,3"
```

JSON.stringify()在将JSON对象序列化为字符串时也用到了ToString，但它并不是严格意义上的强制类型转换。


## 2. ToNumber
[ToNumber](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-tonumber)规则如下图所示：
![](https://upload-images.jianshu.io/upload_images/16527477-32bdfe294e6dc899.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

string的转换规则参考[7.1.3.1](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-tonumber-applied-to-the-string-type "link to this section")

**object的转换**，简单的规则是，Number方法的参数是对象时，将返回NaN，除非是包含单个数值的数组。
```javascript
Number({}) // NaN
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5
Number([])  // 0
```
默认情况下，对象的valueOf方法返回对象本身，所以一般总是会调用toString方法，而toString方法返回对象的类型字符串（比如[object Object]），而根据字符串的解析规则，返回NaN。


## 3. ToBoolean
[ToBoolean](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-toboolean)的转换规则如下：
![](https://upload-images.jianshu.io/upload_images/16527477-c84bf3518cb2f1e4.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有规则可见，所有的对象都返回true，包括`[]`和`{}`。
但这也涉及到一个问题，即**假值对象（falsy object）**。例如：
```javascript
var a = new Boolean(false)
var b = new Number(0)
var c = new String('')

Boolean(a && b && c)   // true  注意此处必须用Boolean来封装
```
可见，falsy值在被封装为对象后，全都返回了true。

#  显示强制类型转换
显示强制类型转换是指代码有明确意图的转换，是显而易见的。

显示转换主要指使用**Number()**、**String()**和**Boolean()**三个函数，手动将各种类型的值，分别转换成数字、字符串或者布尔值。注意，它们前面并没有new，并不会封装为对象。
而上述的三个函数的实现原理，就是使用抽象操作，分别调用**ToNumber()**、**ToString()**和**ToBoolean()**来实现的。

除了 **Number()**、**String()** 和 **Boolean()** 三个函数外，以下的方法也是显示转换：

## toString()
toString()方法能显式地将非字符串类型转换为字符串。一个原始类型能调用toString()方法，是因为在这个过程中，原始类型会**自动装箱（boxing）**，被封装成一个对象，从而有调用其包装对象函数的能力，但是原来那个变量的值不会有任何变化。
```javascript
(23).toString()      // "23"
(function f(){}).toString()    // "function f(){}"
```

## 一元运算符（+ -）
+和 - 0 能显式地将非数字转换为数字。
```javascript
+'3.14'    // 3.14
+-0    // -0
+[]  // 0
{} - 0    // NaN
```
一元运算符里还有像~这样的位操作符，也能起到类型转换的作用，这里先不涉及。

##  parseInt()和parseFloat()
这两个方法是解析字符串中的数字，和将字符串强制类型转换为数字还是有明显区别的。
解析允许字符串中含有非数字字符，解析按从左到右的顺序，如果遇到非数字字符就停止；而转换不允许出现非数字字符，否则会失败并返回NaN。
```javascript
var a = '42px'
Number(a)    // NaN
parseInt(a)    // 42
```
非字符串参数会被隐式转换为字符串，会有各种bug，强烈不建议这么做。传参前请进行类型检查。另外，parseInt一般会指定转换的基数，即使几进制。

详见[我的另一篇文章](https://www.jianshu.com/p/a0a38902be3a)
## !!
这是显式强制转换为布尔值最常用的方法。例如：
```javascript
!!''  // false
!!{}  // true

var a
!!a  // false
```


#  隐式强制类型转换
隐式强制类型转换是指那些隐蔽的，代码在语义上没有明显表明要转换的类型转换。这也是相对的，比如当你看不懂`+[]`的意思时，它对你来说就是隐式的。
隐式转换最大的好处是代码简洁。

##  二元运算符（+）
通过重载，+运算符既能用于数字加法，也能用于字符串拼接。通常的理解是，只要+的一侧有字符串，那么+执行的就是字符串拼接。但是两个数组也是能通过+拼接的，比如：
```javascript
[1, 2, 3] + [4, 5]    // "1,2,34,5"
```
根据ES5的规范，如果其中一个操作数是对象，则首先对其调用ToPrimitive抽象操作，该抽象操作再调用[[DefaultValue]]，以数字作为上下文。这和ToNumber的处理对象的方式一致。
就上例而言，因为数组的valueOf()操作无法得到简单的基本类型值，于是它转而调用toString()，因此呈现出来就是字符串拼接。
而因为+需要调用valueOf()方法，当一个对象中的valueOf()方法被重写的时候，返回值和直接使用String()来转换是不一样的。例如：
```javascript
var a = {
  valueOf: function () { return 100 }
  toString: function () { return 1 }
}

a + ''    // '100'
String(a)    // '1'
```

总之，如果+其中一个操作数是字符串（或者能转换成字符串），则执行拼接，否则执行数字加法。

除了加法运算符（+）有可能把运算子转为字符串，其他运算符都会把运算子自动转成数值。
```javascript
'5' - '2' // 3
'5' * '2' // 10
false - 1 // -1
'1' - 1   // 0
'5' * []    // 0
false / '5' // 0
'abc' - 1   // NaN
null + 1 // 1
undefined + 1 // NaN
```
这里有一个坑，常常被提到，就是
```javascript
[] + {}      // "[object Object]"
{} + []      // 0
```
它们的结果不一样，因为在第一行代码中，`{}`被当做一个空对象，转换为了"[object Object]"，然后进行字符串拼接；在第二行代码中，`{}`被当做一个独立的代码块，已经表示了一段代码的结束，后面的`+ []`被转换为数字0。

更多隐式转换的坑参考：[Js面试题大坑——隐式类型转换](https://blog.csdn.net/itcast_cn/article/details/82887895)
##  隐式转换为布尔值
在以下这些情况中，常常会发生隐式强制类型转换：
1.  `if(...)`语句中的条件判断表达式
2.  `for( .. ; .. ; .. )` 语句中的条件判断表达式
3.   `while(..)`和`do..while(..)`循环中的条件判断表达式
4.  ` ? : `中的条件判断表达式
5.  逻辑运算符`||`和`&&`左边的操作数（作为条件判断表达式）

以上的隐式转换都遵循ToBoolean抽象操作规则。
另外要注意，逻辑运算符`||`和`&&`的返回值是两个操作数中的一个，并不是`true`或者`false`。

#  转换为Object
这个一般出现在面试题中，核心思想是从内存图的角度去考虑。
如下的题目：
```javascript
var a = {n:1}
var b = a 
a.x = a = {n:2}

alert(a.x)返回什么？
alert(b.x)返回什么？
```

思路：
1.  当浏览器读到`a.x = a = {n:2}`时，会先从左至右确定`a`在栈内存中的地址值。
![](https://upload-images.jianshu.io/upload_images/16527477-f06122bd26866687.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以说，此时整个式子中的`a`的值都简写为是100。
2.  然后从右至左进行赋值。`a = {n:2}`的意思是`a`中的地址已经指向了堆内存中的另一个对象，假定它的地址是101，那么`a`作为该地址值得容器，已经变成了101。但式子最左边的`a.x`中`a`的值是提前确定的，仍然是100。
![](https://upload-images.jianshu.io/upload_images/16527477-ce8fe41de1fb89dd.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.  然后是`a.x = a`，将右边的`a`的值赋值给左边`a`的属性`x`，而因为左边`a`的值是100，它所指向的对象里没有`x`，所以现生成`x`。
![](https://upload-images.jianshu.io/upload_images/16527477-e0d55c54e52ece29.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.  而`alert(a)`中的`a`此时已经是101了，而101中并没有属性`x`，所以返回`undefined`，`b.x`指向的是一个对象，alert会调用toString()方法，将对象字符串化返回，即`[object Object]`。

会出现以上原因，是因为对于简单类型的数据来说，赋值就是深拷贝。
对于复杂类型的数据（对象）来说，要区分浅拷贝和深拷贝。当指向同一个对象时，浅拷贝的变量之间会互相影响，深拷贝才能做到相互独立。 深拷贝如何实现会在以后的文章里再探讨。 

#  总结
本文从底层的抽象操作入手，分析了各种数据类型转换的原理。然后分为显示和隐式两个角度看如何转换为number、string和boolean类型。总结转换方法如下：
1.  转为string：
    -  String(x)
    -  x.toString()
    -  x + ''
2.  转为number：
    -  Number(x)
    -  parseInt(x, n)  n为几进制
    -  parseFloat(x)
    -  +x
    -  x - 0
3.  转为boolean：
    -  Boolean(x)
    -  !!x
