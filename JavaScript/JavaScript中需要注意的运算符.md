![](https://upload-images.jianshu.io/upload_images/16527477-7de92853bfc19d5f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.  对象的相加
如果运算子是对象，必须先转成原始类型的值，然后再相加。
```javascript
var obj = { p: 1 };
obj + 2 // "[object Object]2"

// obj的转换过程如下
obj.valueOf().toString() // "[object Object]"
```

###  2.  **比较运算符**
分成两类：相等比较和非相等比较。两者的规则是不一样的，对于非相等的比较，算法是先看两个运算子是否都是字符串，如果是的，就按照字典顺序比较（实际上是比较 Unicode 码点）；否则，将两个运算子都转成数值，再比较数值的大小。
```javascript
'2' > '11111'     // true
5 > '4'    // true
[2] > [11] // true

{ x: 2 } >= { x: 1 } // true
// 等同于 { x: 2 }.valueOf().toString() >= { x: 1 }.valueOf().toString()
// 即 '[object Object]' >= '[object Object]'
```

###  3.  相等运算符：== 和 ===

简单说，它们的区别是相等运算符 `==` 比较两个值是否相等，严格相等运算符 `===` 比较它们是否为“同一个值”。如果两个值不是同一类型，严格相等运算符 `===` 直接返回`false`，而相等运算符 `==` 会将它们转换成同一个类型，再用严格相等运算符进行比较。

####  **复合类型值**

两个复合类型（对象、数组、函数）的数据比较时，不是比较它们的值是否相等，而是比较它们是否指向同一个地址。

```javascript
{} === {} // false
[] === [] // false
(function () {} === function () {}) // false
```

注意，对于两个对象的比较，严格相等运算符比较的是地址，而大于或小于运算符比较的是值。
```javascript
var obj1 = {};
var obj2 = {};

obj1 > obj2 // false
obj1 < obj2 // false
obj1 === obj2 // false
```
上面的三个比较，前两个比较的是值，最后一个比较的是地址，所以都返回`false`。

####  == 涉及的隐式转换
（1）原始类型值

原始类型的值会转换成数值再进行比较。
```javascript
'true' == true // false
// 等同于 Number('true') === Number(true)
// 等同于 NaN === 1

'\n  123  \t' == 123 // true
// 因为字符串转为数字时，省略前置和后置的空格
```

（2）对象与原始类型值比较

对象（这里指广义的对象，包括数组和函数）与原始类型的值比较时，对象转换成原始类型的值，再进行比较。
```javascript
// 对象与数值比较时，对象转为数值
[1] == 1 // true
// 等同于 Number([1]) == 1

// 对象与字符串比较时，对象转为字符串
[1] == '1' // true
// 等同于 String([1]) == '1'
[1, 2] == '1,2' // true
// 等同于 String([1, 2]) == '1,2'

// 对象与布尔值比较时，两边都转为数值
[1] == true // true
// 等同于 Number([1]) == Number(true)
[2] == true // false
// 等同于 Number([2]) == Number(true)
```

（3）undefined 和 null

`undefined`和`null`与其他类型的值比较时，结果都为`false`，它们互相比较时结果为`true`。
```javascript
false == null // false
false == undefined // false

0 == null // false
0 == undefined // false

undefined == null // true
```


详见[JavaScript-Equality-Table](https://dorey.github.io/JavaScript-Equality-Table/)

###  4.  **余数运算符**
运算结果的正负号由第一个运算子的正负号决定。
```  javascript
-1 % 2 // -1
1 % -2 // 1
```
所以，为了得到负数的正确余数值，可以先使用绝对值函数。

###  5.  **&& 和 ||**
`&&` 和 `||` 运算符的返回值并不一定是布尔类型，而是两个操作数的其中之一。
他们首先会对第一个操作数执行条件判断，如果其不是布尔值，就先进行`ToBoolean`强制类型转换，然后再执行条件判断。

`&&` 的运算规则是：如果第一个运算子的布尔值为`true`，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为`false`，则直接返回第一个运算子的值，且不再对第二个运算子求值。
这种跳过第二个运算子的机制，被称为**“短路”**。有些程序员喜欢用它取代`if`结构。
```javascript
if (i) {
  doSomething();
}

// 等价于
i && doSomething();
```
`||` 的运算规则是：如果第一个运算子的布尔值为`true`，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为`false`，则返回第二个运算子的值。

或运算符常用于为一个变量设置默认值。
```javascript
function saveText(text) {
  text = text || '';
  // ...
}

// 或者写成
saveText(this.text || '')
```

###  6.  **? :**
通常来说，三元条件表达式与`if...else`语句具有同样表达效果，前者可以表达的，后者也能表达。但是两者具有一个重大差别，`if...else`是语句，没有返回值；三元条件表达式是表达式，具有返回值。所以，在需要返回值的场合，只能使用三元条件表达式，而不能使用`if..else`。

另外，三元运算符是右结合的，多个三元运算符嵌套，先计算最右边的那个运算符。


###  7.  位运算符
![](https://upload-images.jianshu.io/upload_images/16527477-3e4c00193ef09937.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这些位运算符直接处理每一个比特位（bit），所以是非常底层的运算，好处是速度极快，缺点是很不直观，许多场合不能使用它们，否则会使代码难以理解和查错。

有一点需要特别注意，位运算符只对**整数**起作用，如果一个运算子不是整数，会自动转为整数后再执行。另外，虽然在 JavaScript 内部，数值都是以64位浮点数的形式储存，但是做位运算的时候，是以32位带符号的整数进行运算的，并且返回值也是一个32位带符号的整数。

**最快的取整方法**：`i = i | 0` 或者 `~~i` 或者`i << 0`
**最快的交换方法**：`a^=b; b^=a; a^=b`

####  << 的应用
下面代码使用左移运算符，将颜色的 RGB 值转为 HEX 值。

```javascript
var color = {r: 186, g: 218, b: 85};

// RGB to HEX
// (1 << 24)的作用为保证结果是6位数
var rgb2hex = function(r, g, b) {
  return '#' + ((1 << 24) + (r << 16) + (g << 8) + b)
    .toString(16) // 先转成十六进制，然后返回字符串
    .substr(1);   // 去除字符串的最高位，返回后面六个字符串
}

rgb2hex(color.r, color.g, color.b)
// "#bada55"
```

####  >>> 的应用
查看一个负整数在计算机内部的储存形式，最快的方法就是使用这个运算符。
```javascript
-1 >>> 0 // 4294967295
```
上面代码表示，-1作为32位整数时，内部的储存形式使用无符号整数格式解读，值为 4294967295（即$2^{32}-1$，等于11111111111111111111111111111111）


###  8.  void

`void`运算符的作用是执行一个表达式，然后不返回任何值，或者说返回`undefined`。
这个运算符的主要用途是浏览器的书签工具（Bookmarklet），以及在超级链接中插入代码防止网页跳转。
一个实际的例子，用户点击链接提交表单，但是不产生页面跳转。
```html
<a href="javascript: void(document.form.submit())">
  提交
</a>
```
###9.  逗号运算符
逗号运算符用于对两个表达式求值，并返回后一个表达式的值。
逗号运算符的一个用途是，在返回一个值之前，进行一些辅助操作。
```javascript
var value = (console.log('Hi!'), true);    // Hi!

value // true
```

###  10.  ( )的注意事项
圆括号不是运算符，所以不具有求值作用，只改变运算的优先级。
圆括号之中，只能放置表达式，如果将语句放在圆括号之中，就会报错。
```javascript
(var a = 1)    // SyntaxError: Unexpected token var
```
