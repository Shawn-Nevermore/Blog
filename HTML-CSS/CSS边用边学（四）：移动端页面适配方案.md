![](https://upload-images.jianshu.io/upload_images/16527477-c29b1c622453192a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#  目录
- [目录](#目录)
- [概述](#概述)
- [meta viewport 缩放](#meta-viewport-缩放)
- [媒体查询（Media Queries）](#媒体查询media-queries)
    - [语法](#语法)
    - [逻辑运算符](#逻辑运算符)
    - [媒体类型（Media types）](#媒体类型media-types)
    - [媒体特征（Media features）](#媒体特征media-features)
      - [（1）横屏](#1横屏)
      - [（2）宽高比](#2宽高比)
      - [（3）最大宽度](#3最大宽度)
    - [方案](#方案)
    - [缺点](#缺点)
- [动态 rem](#动态-rem)
    - [rem 计算](#rem-计算)
      - [（1）用 JS 动态计算](#1用-js-动态计算)
      - [（2）媒体查询](#2媒体查询)
    - [缺点](#缺点-1)
- [vw, vh](#vw-vh)
- [总结](#总结)

#  概述
移动端页面，亦即H5页面、手机页面、webview页面等，常因需要适配市面上各种型号各种尺寸的移动设备，而显得繁杂。这里就对常用的移动端页面适配方案做出总结。

注意：本文暂不分析原理，其中涉及到的 visual viewport、layout viewport、物理像素、CSS像素、dpr（device pixel ratio）、dip（device independent pixels）、ppi（pixel per inch）等概念此处不谈。

#  meta viewport 缩放
设置一个理想视口，何为理想？就是 `visual viewport == layout viewport`。在 HTML 中加入如下代码：
```html
<meta name="viewport" content="width=device-width, user-scalable=no, 
initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
```
设置理想视口，可以使得 `layout viewport` 与屏幕宽度 `visual viewport` 一样大，DOM文档主宽度即为屏幕宽度。

这种方法并不是完美方案，需要跟着大量调试CSS，并且一些特大尺寸的元素很难原比例展示，但是在移动端页面刚兴起的时候不失为一种快捷的解决方案。

#  媒体查询（Media Queries）
[媒体查询](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)可以针对不同的屏幕尺寸设置不同的样式，这样允许内容在呈现上针对不同的输出设备进行裁剪，而不必改变内容本身。它添加自CSS3。

先过一遍它的语法和常见属性。

###  语法
```html
<!-- link元素中的CSS媒体查询 -->
<!-- 所有的CSS都会下载，但满足条件的时候才会执行 -->
<link rel="stylesheet" media="(max-width: 800px)" href="example.css" />

<!-- 样式表中的CSS媒体查询 -->
<style>
@media (max-width: 600px) {
  .facet_sidebar {
    display: none;
  }
}
</style>
```
###  逻辑运算符

-  and：`and` 关键字用于合并多个媒体属性或合并媒体属性与媒体类型
-  not：`not` 操作符用来对一条媒体查询的结果进行取反
-  only：`only` 操作符仅在媒体查询匹配成功的情况下被用于应用一个样式，这对于防止让选中的样式在老式浏览器中被应用到，例如：
```html
<link rel="stylesheet" media="only screen and (color)" href="example.css" />
```
-  ,（comma）：媒体查询中使用逗号分隔效果等同于 `or` 逻辑操作符。当使用逗号分隔的媒体查询时，如果任何一个媒体查询返回真，样式就是有效的


###  媒体类型（Media types）
-  all：用于所有设备
-  print：用于打印机和打印预览
-  screen：用于电脑屏幕，平板电脑，智能手机等
-  speech：用于屏幕阅读器等发声设备

###  媒体特征（Media features）
媒体特征描述了用户代理（user agent）、输出设备和运行环境的具体特征。

媒体的特征非常多，这里就不一一列举了，仅举一些有代表性的例子。

#### （1）横屏
```css
@media (orientation: landscape) { ... }
```
#### （2）宽高比
```css
@media (aspect-ratio: 9/18) { ... }  // 目前市面上最常见的比例
```
#### （3）最大宽度
```css
@media only screen and (max-width: 420px) { ... }  // 市面绝大多数手机最大宽度
```
###  方案
通常用媒体查询写响应式布局，需要针对不同的屏幕尺寸写几套CSS，每套的内部各自调整。

[一个例子](https://jsbin.com/rameraj/edit?html,css,js,output)：
![](https://upload-images.jianshu.io/upload_images/16527477-126e07b9d04622b0.gif?imageMogr2/auto-orient/strip)

上面的例子是一个简易的导航栏。此案例中，用 @media 另写了一套CSS，并且写了配套的HTML，就实现了在PC和屏幕宽度小于450px的移动设备的响应式布局。

### 缺点

-  屏幕分辨率分区间：区间内无法进行区分，无法实现100%兼容，一般是用主流分辨率来进行划分
-  额外的工作量：响应式布局的工作都是需要开发者去实现的，带来了额外的开放量
-  不适合功能复杂的页面：响应式一般适合用于资讯类页面，功能复杂的网站对于页面的整体排版和样式要求较高（特别是对比PC和H5）


# 动态 rem

>在W3C官网上是这样描述rem的——“font size of the root element” 。

也就是说，rem 是一个长度单位，是根元素 `<html>` 的字体大小。比如 `<html>` 默认字体是`16px`，那么一个 `2rem` 的字就是 `32px`。

**rem 和 em 的区别：**

-  rem 以根元素字体大小为基准
-  em 确切的讲是一个大写字母 `M` 的宽度，以父元素字体大小为基准

那么我们要做移动端页面适配，只需根据不同屏幕尺寸调整 `<html>` 的 `font-size` 即可。

###  rem 计算
#### （1）用 JS 动态计算
通过JavaScript读取屏幕宽度，然后根据宽度计算出对应的尺寸并设置根元素的 `font-size`。
```javascript
let pageWidth = window.innerWidth    // 视口宽度
document.write(`<style>html{font-size:${pageWidth}px;}</style>`)
```

[一个例子](https://jsbin.com/lumuzis/edit?html,css,output)：

![](https://upload-images.jianshu.io/upload_images/16527477-507d0290dbf4bbf5.gif?imageMogr2/auto-orient/strip)

不过这个例子里以 rem 为单位的元素的数值都是小数，可以优化 `pageWidth` 为原来的 1/10，这样数值基本就是整数了。

####  （2）媒体查询

可以固定设置几个市面上的移动设备常用尺寸：
```css
@media screen and (min-width: 375px){
    html {
        font-size: 14.0625px;   
    }
}
@media screen and (min-width: 360px){
    html {
        font-size: 13.5px;
    }
}
@media screen and (min-width: 320px){
    html {
        font-size: 12px;
    }
}
html {
    font-size: 16px;
}
```

###  缺点

rem 虽然解决了百分比布局中高度和宽度不关联的痛点，但因 rem 与视口宽度并完全等价关联（其实是模拟 vw 的替代方案），所以也存在着宽度不确定、高度不确定的问题，详见：[rem不是神农草，治不了移动端百病](https://imweb.io/topic/5745adf5a94f742c1db63485)。

另外，用 rem 自己手动转换设计稿给的以像素为单位的元素，是十分麻烦的，需要使用转换工具，或者使用 sass 的混合宏来实现 px2rem 的 mixin，上面的例子可改为如下：

```css
@function px( $px ){
  @return $px/$designWidth*10 + rem;
}

$designWidth : 750;  

.child{
  width: px(375);
  height: px(160);
  margin: px(40) px(40);
  border: 1px solid red;
  float: left;
  font-size: 1.2em;
}
```


#  vw, vh
vw（view-width）、vh（view-height） 这两个单位是CSS新增的单位，表示视区宽度/高度，视区总宽度为100vw，总高度为100vh。

这两个单位是直接和视口的宽高挂钩的，是最理想的实现方案，但是兼容性比较差。


#  总结
本文只是对移动端适配的一个粗浅了解，里面涉及的具体原理和实战方面的代码优化并没有详讲。等有了一定移动端开发经验后，我再来补充相关内容。

参考：
-  [移动端适配大法](https://imweb.io/topic/5b8f6a1580495bb24c9217ac)
-  [移动端Web页面适配方案](https://segmentfault.com/a/1190000008767416)
-  [简单粗暴的移动端适配方案 - REM](https://imweb.io/topic/5a523cc0a192c3b460fce3a5)
-  [https://www.w3cplus.com/blog/tags/143.html](https://www.w3cplus.com/blog/tags/143.html)

![加油！](https://upload-images.jianshu.io/upload_images/16527477-841d1688199da23e.gif?imageMogr2/auto-orient/strip)




