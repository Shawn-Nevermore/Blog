![](https://upload-images.jianshu.io/upload_images/16527477-f36b91c5c17ce397.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 目录
- [目录](#目录)
- [基本概念](#基本概念)
- [特点](#特点)
- [flex container 的属性](#flex-container-的属性)
  - [flex-direction](#flex-direction)
  - [flex-wrap](#flex-wrap)
  - [flex-flow](#flex-flow)
  - [justify-content](#justify-content)
  - [align-items](#align-items)
  - [align-content](#align-content)
- [flex item 的属性](#flex-item-的属性)
  - [flex-grow](#flex-grow)
  - [flex-shrink](#flex-shrink)
  - [flex-basis](#flex-basis)
  - [flex](#flex)
  - [order](#order)
  - [align-self](#align-self)
- [案例](#案例)
  - [（1）手机页面](#1手机页面)
  - [（2）产品列表](#2产品列表)
  - [（3）PC 页面布局](#3pc-页面布局)
  - [（4）完美居中](#4完美居中)
- [总结](#总结)

#  基本概念

![](https://upload-images.jianshu.io/upload_images/16527477-f42e839b3e7c7304.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒模型提供最大的灵活性。

任何一个容器都可以指定为 Flex 布局。并且设为 Flex 布局以后，子元素的 `float`、`clear` 和 `vertical-align` 属性将失效。

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

为什么叫主轴和侧轴，不叫x轴和y轴？因为Flex 布局与方向无关，横向纵向可变。

#  特点
1.  Flex 布局与方向无关
2.  Flex 布局可以实现空间自动分配、自动对齐
3.  Flex 布局适用于简单的线性布局，更复杂的布局要交给grid布局


#  flex container 的属性

![](https://upload-images.jianshu.io/upload_images/16527477-6361ead0d8564e34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以上的6个属性，flex-flow 和 align-content 用得较少，更多需要熟练使用另外4个。

##  flex-direction

>flex-direction属性决定主轴的方向（即项目的排列方向）。

有4个值：
-  row（默认值）：主轴为水平方向，起点在左端。
-  row-reverse：主轴为水平方向，起点在右端。
-  column：主轴为垂直方向，起点在上沿。
-  column-reverse：主轴为垂直方向，起点在下沿。

一些示例如下：

![](https://upload-images.jianshu.io/upload_images/16527477-41000e968379ce6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-5f76458f72cfb83c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](https://upload-images.jianshu.io/upload_images/16527477-ba3c9b4f25696e9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-0d3c4fa4f32ff9a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##  flex-wrap
>flex-wrap 属性定义，如果一条轴线排不下，如何换行。

有3个值：

-  nowrap（默认）：不换行。
-  wrap：换行，第一行在上方。
-  wrap-reverse：换行，第一行在下方。

一些示例如下：

![](https://upload-images.jianshu.io/upload_images/16527477-70e697567a9fdd9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-209add2eab0a1274.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##  flex-flow
>flex-flow 属性是 flex-direction 属性和 flex-wrap 属性的简写形式，默认值为 **row nowrap**。
```
.box {
  flex-flow: <flex-direction> || <flex-wrap>;
}
```

##  justify-content
>justify-content 属性定义了项目在主轴上的对齐方式。

它可能取5个值，具体对齐方式与轴的方向有关。下面假设主轴为从左到右。

-  flex-start（默认值）：左对齐
-  flex-end：右对齐
-  center： 居中
-  space-between：两端对齐，项目之间的间隔都相等。
-  space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍

一些示例如下：

![](https://upload-images.jianshu.io/upload_images/16527477-6078f05011f9b5b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-1ddd0f1311976739.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-baf0ed21275b61c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##  align-items
>align-items 属性定义项目在侧轴上如何对齐。

它可能取5个值。具体的对齐方式与侧轴的方向有关，下面假设侧轴从上到下。
-  flex-start：侧轴的起点对齐
-  flex-end：侧轴的终点对齐
-  center：侧轴的中点对齐
-  baseline: 项目的第一行文字的基线对齐
-  stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度

一些示例如下：

![](https://upload-images.jianshu.io/upload_images/16527477-7ec1fff2e977b7a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-af8220f4db617d27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-60f16e51bdcabcd4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-a13c08c889531d3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##  align-content
>align-content 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

有6个值：
-  flex-start：与侧轴的起点对齐
-  flex-end：与侧轴的终点对齐
-  center：与侧轴的中点对齐
-  space-between：与侧轴两端对齐，轴线之间的间隔平均分布
-  space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍
-  stretch（默认值）：轴线占满整个侧轴



#  flex item 的属性

![](https://upload-images.jianshu.io/upload_images/16527477-a927350d96405df6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


以上的6个属性中，用的最多的是 flex-grow 和 order，熟练之后再用 flex。

##  flex-grow
>flex-grow属性定义项目的放大比例，默认为 **0**，即如果存在剩余空间，也不放大。

通俗地讲，flex-grow 是地主家有余粮的时候，几个子女怎么分配，按什么比例。
flex-grow 常用于和 order 配合使用替代“双飞翼布局”，给中间的主体 main 设置 `flex-grow: 1;`，即可撑满整个屏幕，然后分别给 aside，main，nav 设置 order 即可。

![](https://upload-images.jianshu.io/upload_images/16527477-00ecd7680c829c2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##  flex-shrink
>flex-shrink属性定义了项目的缩小比例，默认为 **1**，即如果空间不足，该项目将缩小。

与 flex-grow 相对应地，flex-shrink 表示家里揭不开锅了，子女谁愿意站出来分点粮食给大家

如果一个项目的 flex-shrink 属性为 0，其他项目都为 1，则空间不足时，前者不缩小。“家里的独苗，得好好养着，其他哥哥姐姐把吃的分他点儿，保证他的营养。”

负值对该属性无效。

![](https://upload-images.jianshu.io/upload_images/16527477-84eaedf130ae19d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##  flex-basis
>flex-basis 属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为 **auto**，即项目的本来大小。

它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。

##  flex
>flex 属性是 flex-grow, flex-shrink 和 flex-basis 的简写，默认值为 **0 1 auto**。后两个属性可选。

该属性有两个快捷值：**auto (1 1 auto)** 和 **none (0 0 auto)**。
建议熟练以后优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

![](https://upload-images.jianshu.io/upload_images/16527477-b6f45ac50f4e90b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图在页面变窄时，各子 `div` 的变化：

![](https://upload-images.jianshu.io/upload_images/16527477-f20a06dead84986f.gif?imageMogr2/auto-orient/strip)


##  order
>order属性定义项目的排列顺序。数值越小，排列越靠前，默认为 **0**。

因为默认为 0 ，所以当需要一个 item 放在所有其他 item 左边的时候，可以设置为负。

看如下的例子：

![](https://upload-images.jianshu.io/upload_images/16527477-ee3a3f40a3fe67a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16527477-ee644a328a330f9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##  align-self
>align-self 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 align-items 属性。默认值为 **auto**，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。

有6个可取值，除了 auto，其他都与 align-items 属性完全一致。

![](https://upload-images.jianshu.io/upload_images/16527477-b623e1be280646a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 案例
## （1）手机页面

![](https://upload-images.jianshu.io/upload_images/16527477-e5546fc4e6eb1881.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## （2）产品列表

![](https://upload-images.jianshu.io/upload_images/16527477-cf3310c52c508151.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##  （3）PC 页面布局

![](https://upload-images.jianshu.io/upload_images/16527477-b2cfc250f79e84f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

插一句布局原则：
-  外部 `div` 搞布局，内部 `div` 搞内容，分开写，bug 少
-  需要定宽写死的 `div` 写在最里层，用来自动撑起外层 `div`

##  （4）完美居中

![](https://upload-images.jianshu.io/upload_images/16527477-10fe0d32e795fa69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#  总结

布局的传统解决方案，基于[盒模型](https://developer.mozilla.org/en-US/docs/Web/CSS/box_model)，依赖 [`display`](https://developer.mozilla.org/en-US/docs/Web/CSS/display) 属性 + [`position`](https://developer.mozilla.org/en-US/docs/Web/CSS/position)属性 + [`float`](https://developer.mozilla.org/en-US/docs/Web/CSS/float)属性。它对于那些特殊布局非常不方便，比如，[垂直居中](https://css-tricks.com/centering-css-complete-guide/)就不容易实现。

而Flex 布局的问世，可以简便、完整、响应式地实现各种页面布局，大大提高了我们的开发效率，是未来的趋势。不过，也像文章开头讲到，Flex 布局适用于简单的线性布局，更复杂的布局要交给grid布局。这个以后介绍。

最后，推荐两个小游戏来练习 Flex 布局：
-  [青蛙跳](http://flexboxfroggy.com/#zh-cn "null")
-  [塔防](http://www.flexboxdefense.com/ "null")

![加油！](https://upload-images.jianshu.io/upload_images/16527477-fc7a7cf76184d306.gif?imageMogr2/auto-orient/strip)
