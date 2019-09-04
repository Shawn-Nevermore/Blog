![](https://upload-images.jianshu.io/upload_images/16527477-c21dc4e821fe0986.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#  目录
1. 布局相关属性
    - display
    - position
    - float
2. 各种布局
    - 两栏布局
    - 三栏布局
    - 水平居中
    - 垂直居中
3. 总结

# 概述
本文开始，我想写一个零基础小白的CSS边用边学的系列。因为CSS语法非常简单，所以更多地是自己去踩一个一个的坑，并记录下来时常回顾。而这些坑在书本里并不会告诉你。为了了解如何布局，需要先学习CSS布局的相关知识。这里推荐一个小白快速入门网站：[CSS布局说](http://zh.learnlayout.com)



# 布局相关属性
## 1. display
display 是 CSS 布局中很重要的一个属性，它定义了元素生成的显示框类型，常见的几个属性值有：`block`、`inline`、`inline-block`、`inherit`、`none`、`flex`。`inherit` 表示这个元素从父元素继承 display 属性值；`none` 表示这个元素不显示，也不占用空间位置；`flex` 是 [flex 布局](https://www.yuque.com/fe9/basic/tlk8ck)重要的属性设置，本篇只讲传统的布局方式，这边先介绍前面三个属性值。

每个元素都有默认的 display 属性，比如 `div` 标签的默认 display 属性是 `block`，我们通常称这类元素为块级元素；`span` 标签的默认 display 属性是 `inline`，我们通常称这类元素为行内元素。

块级元素没有设置宽度时，它的宽度是其容器的 100%。可以设置宽高、内边距、外边距等盒模型属性。可以包含块级元素和行内元素。
行内元素行内元素不会独占一行，只会占领自身宽高所需要的空间。给行内元素设置宽高不会起作用，margin 值只对左右起作用，padding 值也只对左右起作用。行内元素一般不可以包含块级元素，只能包含行内元素和文本。

**一个块级元素的高度有什么决定**？结论是：**块级元素的高度由其内部文档流元素的高度总和决定**。
文档流(normal flow)，直译为普通流，是指文档内元素的流动方向，block元素因为默认宽度是其容器的100%，所以它的文档流是从上至下；inline元素无法设置宽高，它的边框是紧贴内容的（注意可替代元素，如`img`，给 `img` 标签设置宽高是可以影响图片大小的），所以它的文档流是从左至右的，如果超过浏览器宽度，则另起一行继续流。

Tips：如果`span`元素中只有一个单词，中间没有空格，那么其内容会一直超出屏幕不换行（或者形成横向滑动条），除非添加属性：`word-break: break all;`（中文网站使用较多）或者`word-break: break word;`。

那么**一个行内元素的高度由什么决定**？结论是：**由字体设计师决定，但一般默认为line-height**。
每种字体有自己的基线，和不同的建议行高，所以包含不同字体的span的高度是不一样的，一个div中如果有多个span，那么div的默认高度就等于最高那个span的高度，这是在行高足够高的情况下成立。具体的细节涉及到**IFC**等内容，推荐：[深入理解 CSS：字体度量、line-height 和 vertical-align](https://zhuanlan.zhihu.com/p/25808995)。

 inline-block 的元素，既具有块级元素可以设置宽高的特性，又具有行内元素不换行的特性。如果在写多个inline-block元素的时候习惯性回车隔开，那么最后呈现的时候两两之间会有空隙，因为浏览器会将 HTML 中的换行符、制表符、空白符合并成空白符，需要设置`vertical-align: top;`。
更多内容推荐：[CSS深入理解vertical-align和line-height的基友关系](https://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/) 以及 [去除inline-block元素间间距的N种方法](https://www.zhangxinxu.com/wordpress/2012/04/inline-block-space-remove-%E5%8E%BB%E9%99%A4%E9%97%B4%E8%B7%9D/)。

## 2. position
在布局中很重要的因素就是定位，[position](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)属性就是用来定义元素的定位机制。position 的常用属性值有：

-  static：默认值，没有定位属性，元素正常出现在文档流中；top/right/down/left无效，可以设置margin来移动
-  relative：相对定位，相对于元素的正常位置进行定位；不脱离文档流，top/right/down/left可能会遮挡附近元素，一般使用margin来移动
-  absolute：绝对定位，相对于除 static 定位以外的元素进行定位，会一直往爸爸爷爷祖宗上面找，直到找到非static定位的元素或者浏览器窗口进行定位；脱离文档流，一般用top/right/down/left来移动，也可以设置margin
-  fixed：固定定位，相对于浏览器窗口进行定位，网站中的固定 header 和 footer 就是用固定定位来实现的
-  inherit：继承父元素的 position 属性值

## 3. float
float 属性定义元素在哪个方向浮动，常用属性值有 left、right，即向左浮动和向右浮动。设置了 float 的元素，会脱离文档流，然后向左或向右移动，直到碰到父容器的边界或者碰到另一个浮动元素。块级元素会忽略 float 元素，文本和行内元素却会环绕它，所以 float 最开始是用来实现文字环绕效果的。

当不给父元素设置宽高时，父元素的宽高会被子元素的内容撑开。但是当子元素设置浮动属性后，子元素会溢出到父元素外，父元素的宽高也不会被撑开了，称之为“高度塌陷”。
解决这个问题便是要**清除浮动**。

这里介绍两种常用的：

1.  after伪元素法
```css
.clearfix::after {
    content: '';
    display: block;  /* 或者 table */
    clear: both;
}
.clearfix {
    zoom: 1;  /* 兼容 IE6 */
}
```
将clearfix类添加到需要浮动的元素的父元素class里即可。

2.  overflow法

给父元素加上一条style `overflow: auto`即可。如果子元素内容太多，被修剪，则会出现滚动条，另一种`overflow: hidden;`则是当子元素内容超过父元素高度，则被隐藏。

更多清除浮动详情请见[clearfix](https://stackoverflow.com/questions/211383/what-methods-of-clearfix-can-i-use)。
Tips：内联元素浮动之后默认display: block。

<br>

# 各种布局
## 两栏布局

基础代码：
```html
<div class="container">
    <div class="left">left</div>
    <div class="right">right</div>
</div>
```
```css
.left {
    width: 100px;
    height: 150px;
    background-color: #FFB5BF;
}
.right {
    height: 150px;
    background-color: #94E8FF;
}
```
预计效果：

![](https://upload-images.jianshu.io/upload_images/16527477-f6f952b6346d7a6c.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面介绍几种两栏布局方法：

####  1.  设置 display 为 inline-block
```css
.container {
    font-size: 0;    /* 消除间距 */
}
.left, .right {
    display: inline-block;
}
.right {
    width: calc(100% - 100px);   /* 计算宽度，运算符号左右一定要有空格 */
}
```
####  2. 使用 float

处于文档流中的块级元素无法感知到浮动元素的存在，如果设置 .left 为 左浮动，.right 会当 .left 不存在，由于块级元素的默认宽度是父级元素的 100%，此时 .right 的宽度就已经是 100% 了，但是文字仍会环绕。别忘了设置 .right 的 margin 值来给 .left 预留空间。
```css
.left {
    float: left;
}
.right {
    margin-left: 100px;   /* 为 .left 留出空间 */
}
.container {
    overflow: hidden;    /* 别忘了清除浮动 */
}
```
我们可以还设置 .left、.right 均左浮动，这时，它们便会紧贴着排列在一行。因为 .right 是浮动的，所以需要计算宽度。
```css
.left {
    float: left;
}
.right {
    float: left;
    width: calc(100% - 100px);
}
.container {
    overflow: hidden;
}
```
.left 浮动的时候，.right 会无视 .left，有没有不无视，留出位置的可能？有的，让 .right 形成 BFC，.right 就不会和 .left 重合了。BFC 不会忽视浮动元素，这也是它的特点之一。
```css
.left {
    float: left;
}
.right {
    overflow: auto;    /* 形成 BFC */
}
.container {
    overflow: hidden;
}
```
关于BFC，这里不做详述，请见[BFC](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)。

####  3. 用 absolute
```css
.container {
    position: relative;
}
.left {
    postion: absolute;
}
.right {
    margin-left: 100px;
}
```


## 三栏布局

三栏布局中耳熟能详的便是圣杯布局和双飞翼布局了。圣杯布局来源于2006年的一篇文章：[In Search of the Holy Grail](https://alistapart.com/article/holygrail)。双飞翼布局始于淘宝 UED。两者都是在解决两边固定宽度，中间自适应的三栏布局，并且主要内容要优先渲染，按照 DOM 从上至下的加载原则，中间的自适应部分要放在前面。

基础代码：
```html
<div class="container">
    <!-- 保证优先渲染 -->
    <div class="center">center</div>       
    <div class="left">left</div>
    <div class="right">right</div>
</div>
```
```css
body {
    min-width: 630px;
}
.center {
    width: 100%;
    height: 150px;
    background-color: #94E8FF;
}
.left {
    width: 100px;
    height: 150px;
    background-color: #FFB5BF;
}
.right {
    width: 200px;
    height: 150px;
    background-color: #8990D5;
}
```
预计效果：

![](https://upload-images.jianshu.io/upload_images/16527477-1b6589f152ba156a.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1. 圣杯布局
思路： 
1.  三个div都设置浮动`float: left;`，.container 清除浮动。
2.  因为 .center 的宽度是 100%，所以 .left 和 .right 排在了第二行，可以理解为排在了 .center 的后面。这个时候，.left 要回到 .center 的最左边，便是要向左移动 .center 的宽度，即 100%，.left 移动了之后，.right 会自动补上 .left 的空位，此时，.right 想要达到 .center 的最右边，只需要向左移动它自己本身的宽度就可以了，即 200px
3. 此时 .center 的文字被遮挡了，.left、.right 都覆盖在 .center 的上面，这里先设置父元素 .container 的 padding 属性，给 .left、.right 留出空间，即`padding-left: 100px;`，`padding-right: 200px;`
4.  由于父元素设置了 padding，所有子元素都往中间挤了，此时只需将 .left、.right 分别向左向右拉到准备的空位就好了。首先将定位属性设置为 relative，即相对自己定位，.left 要向左移动 100px，.right 要向右移动 200px，所以 .left 只要设置 left: -100px; 、.right 设置 right: -200px; 便能达到效果。

```css
body {
    min-width: 630px;
}

.container {
    overflow: hidden;
    padding-left: 100px;
    padding-right: 200px;
}

.center {
    width: 100%;
    height: 150px;
    background-color: #94E8FF;
    float: left;
}

.left {
    width: 100px;
    height: 150px;
    background-color: #FFB5BF;
    float: left;
    margin-left: -100%;
    position: relative;
    left: -100px;
}

.right {
    width: 200px;
    height: 150px;
    background-color: #8990D5;
    float: left;
    margin-left: -200px;
    position: relative;
    right: -200px;
}
```
圣杯布局的核心思想是使用浮动布局，用 padding 为左右元素留空间，灵活使用 margin 的负值和相对定位让元素移动到相应的位置。
#### 2. 双飞翼布局
双飞翼布局与圣杯布局的前部分一样，给左右两边元素留出位置的思路是：多加了一个 div，将中间自适应部分包裹起来，利用子 div 的 margin 来给左右元素留空间。CSS部分更加简洁。
```html
<div class="container">
    <div class="center-container">
        <div class="center">center</div>
    </div>
    <div class="left">left</div>
    <div class="right">left</div>
<div>
```
```css
body {
    min-width: 630px;
}
.container {
    overflow: hidden;
}
.center-container {
    width: 100%;
    float: left;
}
.center-container .center {
    height: 150px;
    background-color: #94E8FF;

    margin-left: 100px;        /* 新添加的属性 */
    margin-right: 200px;       /* 新添加的属性 */
}
.left {
    width: 100px;
    height: 150px;
    background-color: #FFB5BF;
    float: left;
    margin-left: -100%;
}
.right {
    width: 200px;
    height: 150px;
    background-color: #8990D5;
    float: left;
    margin-left: -200px;
}
```
参考：[CSS布局实践](https://www.yuque.com/fe9/basic/ecdg1z#9qpxlo)
还有一些比较粗暴的方法，因为有各种尺寸限制的bug，就不列举了。


## 水平居中
水平居中分为块级元素居中和行内元素居中。
#### 1. 行内元素居中：
只需要给其父元素添加属性：
```css
text-align: center;
```
Tips：块级元素中的文字也会随着居中，但位置不变。

#### 2. 块级元素居中：
**定宽：**
```css
margin: 0 auto;
```
**不定宽：**参考[不定宽居中](https://blog.csdn.net/wen2017/article/details/83031692)

## 垂直居中
#### 1. 文字垂直居中：
单行文字的垂直居中，只要将行高与容器高设为相等即可，即 height = line-height。
如果有n行文字，那么将行高设为容器高度的n分之一即可。
#### 2. 块级元素居中：
建议方案：父元素不写死 height，然后用内部块级元素的上下 margin 去撑开，上下 margin 相等即可达到居中效果。这样子元素的内容可以任意增删不影响居中布局。

另外，通过 `vertical-align:middle` 实现CSS垂直居中是常使用的方法，但是有一点需要格外注意，vertical生效的前提是元素的 `display：inline-block`。

还有非常多奇技淫巧的居中方法，这里贴出博客拓展阅读：
-  [CSS垂直居中的11种实现方式](https://www.cnblogs.com/zhouhuan/p/vertical_center.html)

-  [大小不固定的图片、多行文字的水平垂直居中](https://www.zhangxinxu.com/wordpress/2009/08/%E5%A4%A7%E5%B0%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A%E7%9A%84%E5%9B%BE%E7%89%87%E3%80%81%E5%A4%9A%E8%A1%8C%E6%96%87%E5%AD%97%E7%9A%84%E6%B0%B4%E5%B9%B3%E5%9E%82%E7%9B%B4%E5%B1%85%E4%B8%AD/)
-  [七种垂直居中的方法](https://jscode.me/t/topic/1936)

最后，介绍一个神奇的网站：[How to Center In Css](http://howtocenterincss.com/)，一键式解决所有烦恼。

# 总结

想说一下我的初衷：为什么叫边用边学。当你刚知道选择器是啥的时候，如果一上来给你一个任务：用一个div实现一个纯CSS的会旋转的阴阳八卦，大概是这样：[阴阳八卦](https://uiao.info/YinYang/index.html)，你一定是一脸懵逼的，但如果慢慢去把文档过一遍再动笔，大概需要3天看完，两周消化，一个月熟悉。不妨先做着，写的过程中，通过不断地模仿，查文档，读博客，和使用generator生成的效果代码，会让你成就感爆棚。

我觉得学CSS应该像学英语一样，先记些简单的句子表达，接着立马去实践，遇到不会说的地方再回去查漏补缺，而不是一开始抱着牛津字典从第一页开始背。包括之前学算法，一上来抱着算法导论开始啃，一头包，并逐渐劝退。

这篇里讲的是比较经典的布局方式，个人觉得最有价值的是学会双飞翼布局，这里没有用到的像flex，grid，column等布局方式，以后会慢慢研究。
