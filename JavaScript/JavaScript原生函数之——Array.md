![](https://upload-images.jianshu.io/upload_images/16527477-e63e9a25b08a4107.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#  目录
1.  数组的本质
2.  Array构造方法
3.  Array实例方法
    -  join()
    -  concat()
    -  reverse()
    -  slice()
    -  splice()
    -  sort()
    -  map()
    -  forEach()
    -  filter()
    -  some() 和 every()
    -  reduce() 和 reduceRight()
4.  总结


#  数组的本质
在讲Js原生函数Array之前，我们先探讨一下数组的本质。先给出**结论：数组是一个原型链上包含Array.prototype的对象。**

这就是区分数组和**伪数组**的唯一标志。常见的伪数组，比如：
-  arguments 对象
-  document.querySelectAll('div') 返回的对象
-  字符串

它们都有与数组几乎一样的属性，可以造出来 0,1,2,3,4,5...n,length 这些 key，能循环遍历，能自己改写`valueOf`和`toString`方法来实现数组差不多的功能，等等。但是它们无法直接调用`Array.prototype`中的方法，比如`push`，`pop`，`shift`，`unshift`……因为它们是Object构造出来的，原型链上没有这些方法。

要想调用Array.prototype中的方法，两条路：
1.  使用数组的`slice`方法将“类似数组的对象”变成真正的数组
2.  `Array.prototype.join.call(arrayLike, ',')`  （`join`只是其中一种方法，调用比如forEach可在第二个参数传入自定义的`print`方法来实现遍历输出）

以上的两种方法中，第二种的效率比直接使用原生数组的`forEach`要慢，建议第一种。

其次，数组的`length`是一个动态属性，等于键名中的最大整数加上1；而伪数组的length并不会随着值的添加而改变，需要自己设置。

另外数组也可以添加非数字值的key，比如各种奇怪的字符串。和狭义的对象一样，数组的键名其实也是字符串。之所以可以用数值读取，是因为非字符串的键名会被转为字符串。也就是说数组本质是对象的一种，由`typeof`的返回值也可见。

#  Array构造方法
Array是 JavaScript 的原生对象，同时也是一个构造函数，可以用它生成新的数组。
###  （1）一个参数
![](https://upload-images.jianshu.io/upload_images/16527477-2f147185db744863.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由上图可见，当只传入一个3的时候，表示数组的`length`为3，而数组内是`empty × 3`，虽然`a[0]`返回`undefined`，但是数组中并没有`'0'`这个key。

另外，对于复杂类型，有new没有new一样，只是语义上的区别，没有new表示封装为对象，有new表示生成一个对象。基本类型有new表示生成对象，没有new还是原来的类型，仅取值。
```javascript
var arr = Array(3)
typeof arr  // "object"

var a = String('123')
typeof a    // "string"

var b = new String('123')
typeor b    //  "object"
```

传入非正整数数字参数，会有其他结果：
```javascript
// 非正整数的数值作为参数，会报错
new Array(3.2) // RangeError: Invalid array length
new Array(-3) // RangeError: Invalid array length

// 单个非数值（比如字符串、布尔值、对象等）作为参数，
// 则该参数是返回的新数组的成员
new Array('abc') // ['abc']
new Array([1]) // [Array[1]]
```
###  （2）多个参数
```javascript
var arr = new Array(3, 3)
arr  // [3, 3]
```
传入多个参数时，所有参数都是返回的新数组的成员。

可以看到，Array作为构造函数，行为很不一致。因此，不建议使用它生成新数组，直接使用数组字面量是更好的做法。



#  Array实例方法
实例方法是定义在Array.prototype上的方法，每个实例对象都会继承到。
常用的：
-  valueOf：返回数组本身
-  toString：返回数组的字符串形式（二维及以上会展开）
-  indexOf：返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1
-  push：用于在数组的末端添加一个或多个元素，并返回添加新元素后的`length`
-  pop：用于删除数组的最后一个元素，并返回该元素
-  shift：用于删除数组的第一个元素，并返回该元素
-  unshift：用于在数组的第一个位置添加一个或多个元素，并返回添加新元素后的数组长度

注意：以上四种增删数组的方法均**会改变原数组**。

###  join()
`join()`方法以指定参数作为分隔符，将所有数组成员连接为一个字符串返回。如果不提供参数，默认用逗号分隔。
```javascript
var a = [1, 2, 3, 4];

a.join(' ') // '1 2 3 4'
a.join(' | ') // "1 | 2 | 3 | 4"
a.join() // "1,2,3,4"

['a',, 'b'].join('-')
// 'a--b'  
//  空位转成空字符串
```
###  concat()
`concat`方法用于多个数组的合并。它将新数组的成员，添加到原数组成员的后部，然后返回一个新数组，原数组不变。

```javascript
var a = ['hello']
a.concat(['world'], ['!'])  // ["hello", "world", "!"]
a  // ['hello']
```
数组的浅拷贝：
```
var a = [{'n': 1}, {'s': 'abc'}]
var newArr = a.concat()

a[0].n = 2
newArr[0].n  // 2
```
###  reverse()
`reverse`方法用于颠倒排列数组元素，返回改变后的数组。注意，该方法**将改变原数组**。

###  slice()
`slice`，即“切片”，该方法用于提取目标数组的一部分，返回一个新数组，原数组不变。
可传入两个参数，第一个参数为起始位置，第二个参数为终止位置（但该位置的元素本身不包括在内）
```javascript
var a = ['a', 'b', 'c']

//  起始位置是1，末尾位置省略，表示直到末尾
a.slice(1) // ["b", "c"]

a.slice(1, 2) // ["b"]

//  第二个参数超过length也可以
a.slice(2, 6) // ["c"]

//  等于返回一个原数组的拷贝
a.slice() // ["a", "b", "c"]

//  负数表示倒数计算的位置
a.slice(-2) // ["b", "c"]

//  注意这里不包括-1位置的元素
a.slice(-2, -1) // ["b"]

//  如果第一个参数大于等于数组长度，或者第二个参数小于第一个参数，则返回空数组
a.slice(4) // []
a.slice(2, 1) // []
```

###  splice()
`splice`方法用于删除原数组的一部分成员，并可以在删除的位置添加新的数组成员，返回值是被删除的元素。注意，该方法**会改变原数组**。

语法：
```javascript
arr.splice(start, count, addElement1, addElement2, ...)
```
`start`起始位置，`count`删掉几个元素，之后的全是添加的新元素。
```javascript
var a = ['a', 'b', 'c', 'd', 'e', 'f']

a.splice(4, 2, 1, 2) // ["e", "f"]
a // ["a", "b", "c", "d", 1, 2]

a.splice(-4, 2)  // ["c", "d"]
a  // ["a", "b", 1, 2]

//  count设置为0，就表示只添加元素
a.splice(2, 0, "c")  // []
a  // ["a", "b", "c", 1, 2]

//  只设置start，就表示从start开始切分为两个数组
a.splice(3)  //  [1, 2]
a  // ["a", "b", "c"]
```

###  sort()
`sort`方法对数组成员进行排序，默认是按照字典顺序排序，底层原理是快排。排序后，**原数组将被改变**。
```javascript
[10111, 1101, 111].sort()    // [10111, 1101, 111]
```
如果想让`sort`方法按照自定义方式排序，可以传入一个函数作为参数。
```javascript
[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o1.age - o2.age;  //  表示按年纪从小到大，反之是从大到小
})
// [
//   { name: "李四", age: 24 },
//   { name: "王五", age: 28  },
//   { name: "张三", age: 30 }
// ]
```

###  map()
`map`方法将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回。该函数调用时，map方法向它传入三个参数：当前成员、当前位置和数组本身。可以选择性省略参数。
```javascript
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
})
// [0, 2, 6]
```

用`map()`可以非常简洁的实现一些小功能，比如：
```javascript
// 所有数字取平方
[1, 2, 3].map(v => v ** 2)    // [1, 4, 9]  

// 所有数字转字符串
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9]
arr.map(String)     // ['1', '2', '3', '4', '5', '6', '7', '8', '9']
```

`map`方法不会跳过`undefined`和`null`，但是会跳过空位。
###  forEach()
`forEach`方法与`map`方法很相似，也是对数组的所有成员依次执行参数函数。但是，`forEach`方法不返回值，只用来操作数据。
`forEach`的用法与`map`方法一致，参数是一个函数，该函数同样接受三个参数：当前值、当前位置、整个数组。
```javascript
[2, 5, 9].forEach(function(elem, index, arr){
    console.log('[' + index + '] = ' + element)
})
// [0] = 2
// [1] = 5
// [2] = 9
```

注意，`forEach`方法无法中断执行，总是会将所有成员遍历完。如果希望符合某种条件时，就中断遍历，要使用for循环。

`forEach`方法不会跳过`undefined`和`null`，但会跳过空位。
###  filter()
`filter`方法用于过滤数组成员，满足条件的成员组成一个新数组返回。
它的参数是一个函数，所有数组成员依次执行该函数，返回结果为`true`的成员组成一个新数组返回。该方法不会改变原数组。也就是说，这个函数就是过滤规则。
```javascript
[1, 2, 3, 4, 5].filter(v => v%2 === 0)    // 筛选偶数
```
`filter`方法的参数函数可以接受三个参数：当前成员，当前位置和整个数组。

###  some() 和 every()
这两个方法类似“断言”（assert），返回一个布尔值，表示判断数组成员是否符合某种条件。

它们接受一个函数作为参数，所有数组成员依次执行该函数。该函数接受三个参数：当前成员、当前位置和整个数组，然后返回一个布尔值。

`some`方法是只要一个成员的返回值是`true`，则整个`some`方法的返回值就是`true`，否则返回`false`。`every`方法是所有成员的返回值都是`true`，整个`every`方法才返回`true`，否则返回`false`。有点 `||` 和 `&&` 的意思。

```javascript
var arr = [1, 2, 3, 4, 5]
arr.some(v => v >= 3)  // true
arr.every(v => v >= 3)  // false
```

注意，对于空数组，`some`方法返回`false`，`every`方法返回`true`，回调函数都不会执行。

###  reduce() 和 reduceRight()
`reduce`方法和`reduceRight`方法依次处理数组的每个成员，最终累计为一个值。它们的差别是，`reduce`是从左到右处理（从第一个成员到最后一个成员），`reduceRight`则是从右到左（从最后一个成员到第一个成员），其他完全一样。

`reduce`方法和`reduceRight`方法的第一个参数都是一个函数。该函数接受以下四个参数：
1.  累积变量，默认为数组的第一个成员
2.  当前变量，默认为数组的第二个成员
3.  当前位置（从0开始）
4.  原数组

这四个参数之中，只有前两个是必须的，后两个则是可选的。

如果要对累积变量指定初值，可以把它放在`reduce`方法和`reduceRight`方法的第二个参数。

例子：找出字符长度最长的数组成员
```javascript
function findLongest(entries) {
  return entries.reduce(function (longest, entry) {
    return entry.length > longest.length ? entry : longest
  }, '');
}

findLongest(['aaa', 'bb', 'c']) // "aaa"
```

例子：计算所有奇数的和
```javascript
var a = [1,2,3,4,5,6,7,8,9]
a.reduce((sum, n) => n % 2 === 1 ? sum + n : sum)    // 25
```

# 总结
原生函数Array作为Js的标准库之一，其API非常重要，记住常用的API能让我们事半功倍地实现很多功能。并且，上面这些数组方法之中，有不少返回的还是数组，所以可以链式使用，比如筛选数据库中的email并且遍历输出，等等。  

