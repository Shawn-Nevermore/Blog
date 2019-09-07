![](https://upload-images.jianshu.io/upload_images/16527477-9c969b7e02b631da.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 目录

1.  [概述](#%e6%a6%82%e8%bf%b0-%e2%86%91e79baee5bd95)
2.  [Number](#number)
3.  [String](#string)
4.  [Boolean](#string)
5.  [Null 和 Undefined](#null-%e5%92%8c-undefined)
6.  [Object](#object)
7.  [总结](#%e6%80%bb%e7%bb%93)

# 概述

JavaScript 是一种弱类型或者说动态语言。这意味着你不用提前声明变量的类型，在程序运行过程中，类型会被自动确定。这也意味着你可以使用同一个变量保存不同类型的数据。

ECMAScript 标准定义了 7 种数据类型：

-   Number：整数和小数
-   String：文本字符串
-   Boolean：表示真伪的两个特殊值，即 true（真）和 false（假）
-   Undefined：表示“未定义”或不存在，即由于目前没有定义，所以此处暂时没有任何值
-   Null：表示空值，即此处的值为空
-   Object：各种值组成的集合
-   Symbol：参见[Symbol](http://es6.ruanyifeng.com/#docs/symbol)，本文不讨论

以上的数据类型中，Object 以外的都称作原始类型，都是不可变的（值本身无法被改变），JavaScript 中字符串是不可变的，因为对字符串的操作一定返回了一个新字符串，原始字符串并没有被改变。Object 被称为合成类型或者复杂类型。

# Number

[↑](#%e7%9b%ae%e5%bd%95)

JavaScript 内部，所有数字都是以 64 位浮点数形式储存。比如 1 与 1.0 是相同的，JavaScript 语言的底层根本没有整数，所有数字都是 64 位浮点数。但由于浮点数不是精确的值，所以整数间的四则运算往往得不到想要的值，需要注意。

这 64 位二进制数如下：
第 1 位：符号位，0 表示正数，1 表示负数
第 2 位到第 12 位（共 11 位）：指数部分
第 13 位到第 64 位（共 52 位）：小数部分（即有效数字）

符号位决定了一个数的正负，指数部分决定了数值的大小（$2^{1024}$ 到 $2^{-1023}$），小数部分决定了数值的精度（$-2^{53}$ 到 $2^{53}$，有效数字的第一位默认总是 1，不保存在 64 位浮点数之中）。如果一个数大于 $2^{1024}$，就表示为 Infinity，小于 $2^{-1075}$，返回 0。

JavaScript 对整数提供四种进制的表示方法：十进制、十六进制、八进制、二进制。

-   十进制：没有前导 0 的数值。
-   八进制：有前缀 0o 或 0O 的数值，或者有前导 0、且只用到 0-7 的八个阿拉伯数字的数值。
-   十六进制：有前缀 0x 或 0X 的数值。
-   二进制：有前缀 0b 或 0B 的数值。

## Number 中的特殊数值

#### +0 和-0

JavaScript 的 64 位浮点数之中，有一个二进制位是符号位。这意味着，任何一个数都有一个对应的负值。绝大多数情况下，+0 和-0 作用几乎一样，仅被当做分母时，不一样：

```javascript
1 / +0 // Infinity
1 / -0 // -Infinity
1 / +0 === 1 / -0 // false
```

为什么需要 -0 ？
有些应用程序中的数据需要以级数形式来表示（比如动画帧的移动速度），数字的符号位用来代表其他信息（比如移动的方向）。此时如果一个值为 0 的变量失去了它的符号位，它的方向信息就会丢失。所以保留 0 值的符号位可以防止这类情况的发生。

#### NaN

NaN（Not a Number），虽然名字叫“我不是数字”，但是：

```javascript
typeof NaN // "number"
NaN === NaN // false
```

NaN 与任何数（包括它自己）的运算，得到的都是 NaN。NaN 是布尔运算为 false 的五个值之一。

#### Infinity

Infinity 表示“无穷”，用来表示两种场景。一种是一个正的数值太大，或一个负的数值太小，无法表示；另一种是非 0 数值除以 0，得到 Infinity。

```javascript
Math.pow(2, 1024) // Infinity

0 / 0 // NaN
1 / 0 // Infinity
```

Infinity 与 NaN 比较，总是返回 false。Infinity 与 undefined 计算，返回的都是 NaN。
Infinity 的四则运算，符合无穷的数学计算规则，但有几个需要注意：

```javascript
0 * Infinity // NaN
Infinity - Infinity // NaN
Infinity / Infinity // NaN
null * Infinity // NaN
null / Infinity // 0
Infinity / null // Infinity
```

Infinity 与 null 计算时，null 会转成 0，等同于与 0 的计算。

## 与数值相关的全局方法

#### parseInt()

parseInt 方法用于将字符串转为整数。具体规则见例子：

```javascript
parseInt('   81') // 81
parseInt(1.23) // 1      这里会先将1.23转化为字符串
parseInt('15px') // 15
parseInt('abc') // NaN
parseInt('0x10') // 16
parseInt('011') // 11
parseInt('011', 8) // 9
parseInt('1546', 2) // 1
parseInt('546', 2) // NaN
```

如果第二个参数不是数值，会被自动转为一个整数。这个整数只有在 2 到 36 之间，才能得到有意义的结果，超出这个范围，则返回 NaN。如果第二个参数是 0、undefined 和 null，则直接忽略。

#### parseFloat()

parseFloat 方法用于将一个字符串转为浮点数。具体规则见例子：

```javascript
parseFloat('314e-2') // 3.14
parseFloat('3.14more non-digit characters') // 3.14
parseFloat('\t\v\r12.34\n ') // 12.34
parseFloat([]) // NaN
parseFloat('FF2') // NaN
parseFloat('') // NaN
```

# String

JavaScript 的字符串类型用于表示文本数据。它是一组 16 位的无符号整数值的“元素”。JavaScript 对 UTF-16 的支持是不完整的，由于历史原因，只支持两字节的字符，不支持四字节的字符。所以后来新增的某些字超过了两个字节，在 js 中会用四个字节表示，其 length 为 2。因此，JavaScript 返回的字符串长度可能是不正确的。

#### 转义：

```javascript
'Did she say \'Hello\'?'
'C:\\迅雷下载\\你的老师们.avi'
'\251' // "©"  反斜杠后面紧跟三个八进制数（000到377），代表一个字符
'\xA9' // "©"  \x后面紧跟两个十六进制数（00到FF），代表一个字符
'\u00A9' // "©"  \u后面紧跟四个十六进制数（0000到FFFF），代表一个字符
'\a' // "a"  a是正常字符，\被忽略
```

#### 多行字符串：

```javascript
var a =
    '12345 \
         67890' // 注意，反斜杠的后面必须是换行符，而不能有其他字符（比如空格），否则会报错。
//  并且第二行左边的空格是算在字符串里面的，length会有所增加

var b = '12345' + '67890'

var c = `12345
         67890` // 反引号也能实现多行，也要算空格
```

#### 字符串与数组：

字符串内部的单个字符无法改变和增删，这些操作会默默地失败。但是，可以基于对原始字符串的操作来创建新的字符串。例如：

-   获取一个字符串的子串可通过选择个别字母或者使用  [`String.substr()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/substr 'substr() 方法返回一个字符串中从指定位置开始到指定字符数的字符。').
-   两个字符串的连接使用连接操作符 (`+`) 或者  [`String.concat()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/concat 'concat() 方法将一个或多个字符串与原字符串连接合并，形成一个新的字符串并返回。').

```javascript
var s = 'hello'
s[0] // "h"
s[1] // "e"
s[4] // "o"

// 直接对字符串使用方括号运算符
'hello'[1] // "e"

delete s[0]
s // "hello"

s[1] = 'a'
s // "hello"

s[5] = '!'
s // "hello"

s.length = 3
s.length // 5
```

# Boolean

布尔表示一个逻辑实体，可以有两个值：true 和 false。
下面六个值在布尔运算中被转为 false，其他值都视为 true。

-   `undefined`
-   `null`
-   `false`
-   `0`
-   `NaN`
-   `""`或`''`（空字符串）

# Null 和 Undefined

null 与 undefined 都可以表示“没有”，含义非常相似。
区别是这样的：null 是一个表示“空”的对象，转为数值时为 0；undefined 是一个表示"此处无定义"的原始值，转为数值时为 NaN。

-   （规范）如果一个变量没有被赋值，那么这个变量的值就是 undefiend
-   （习俗）如果你想表示一个还没赋值的对象，就用 null。如果你想表示一个还没赋值的字符串/数字/布尔/symbol，就用 undefined（但是实际上你直接 var xxx 一下就行了，不用写 var xxx = undefined）

```javascript
Number(null) // 0
5 + null // 5    null转为数字时，自动变成0
Number(undefined) // NaN
5 + undefined // NaN
```

# Object

简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。

## 键名（属性）

对象的所有键名都是字符串（ES6 又引入了 Symbol 值也可以作为键名），所以加不加引号都可以。如果键名是数值，会被自动转为字符串。
如果属性（property，也就是键名）的值还是一个对象，就形成了链式引用。
属性可以动态创建，不必在对象声明时就指定。

如果不同的变量名指向同一个对象，那么它们都是这个**对象的引用**，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有变量。如果需要一个变量的改变不影响其他变量，那么需要[深拷贝](https://flaviocopes.com/how-to-clone-javascript-object/)，以及参考：[什么是 js 深拷贝和浅拷贝及其实现方式](https://www.haorooms.com/post/js_copy_sq)和[深拷贝与浅拷贝的实现](http://www.alloyteam.com/2017/08/12978/)。
上面的问题一般需要结合计算机内存以及 GC 来分析。

**读取对象的属性**，有两种方法，一种是使用点运算符，还有一种是使用方括号运算符。
如果键名不符合标识名的条件，且也不是数字，则必须加上引号，否则会报错。如`'1$'`，`'m+n'`，`'p q'`等，但是`object['']`是合法的，中文变量名也是合法的。

注意 `object.key` 与 `object[key]` 不同：如果使用方括号运算符，键名必须放在引号里面，否则会被当作变量处理。

```javascript
var foo = 'bar'

var obj = {
    foo: 1,
    bar: 2
}

obj.foo // 1
obj[foo] // 2
```

方括号运算符内部还可以使用表达式。

```javascript
obj['hello' + ' world']
obj[3 + 3]
```

**属性的查看**：Object.keys()

#### delete 命令

delete 命令用于删除对象的属性，删除成功后返回 true。
注意，删除一个不存在的属性，delete 不报错，而且返回 true。因此，不能根据 delete 命令的结果，认定某个属性是存在的。

```javascript
var obj = {}
delete obj.p // true
```

delete 命令只能删除对象本身的属性，无法删除继承的属性。

delete 一个属性和将属性设置为 undefined 的**区别**：

-   delete 会删除掉对象的属性和属性对应的值，即无 key 无 value
-   后者相当于清空属性的 value，但是 key 本身还在

#### for...in 和 hasOwnProperty()

in 运算符用于检查对象是否包含某个属性，如果包含就返回 true，否则返回 false。但是不能识别哪些属性是对象自身的，哪些属性是继承的。

for...in 循环用来遍历一个对象的全部属性。它有两个使用注意点：

-   它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性（比如 Object.toString）。
-   它不仅遍历对象自身的属性，还遍历继承的属性。

如果继承的属性是可遍历的，那么就会被 for...in 循环遍历到，所以使用 for...in 的时候，应该结合使用 hasOwnProperty 方法，在循环内部判断一下，某个属性是否为对象自身的属性。

# 总结

数据类型是编程的基石，搞清楚原始数据类型相关的常用全局方法，对象的构成、常用属性、标准库，以及他们在内存中的不同表现，是非常重要的。本篇中的 Object 是指狭义的对象，这里没有提到的两个广义对象的分支 Function 和 Array，以及不同类型之间的运算牵扯出来的隐式转换，接下来将会涉及到。
