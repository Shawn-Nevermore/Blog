![](https://upload-images.jianshu.io/upload_images/16527477-fa4579e3ad080adb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 前言

如果你的代码中经常出现 `width` 和 `height` 写死的情况，那么你的 CSS 代码多多少少都有一些问题。而且，CSS 中关于宽度和高度的知识，有一些让人摸不着头脑的坑，这里就集中总结一下。

本文默认以最有代表性的内联元素 `span` 和 最有代表性的块级元素 `div` 为例，其他内联元素和块级元素基本符合本文的规律。

# 内联元素的宽高

内联元素的**高度**有什么决定？**结论是：由字体设计师决定，但一般默认为 `line-height`**。

`line-height` 的默认值是 `normal`， 这是个不确定的值。我们知道 `line-height` 的值是数字时表示相对于 `font-size` 的倍数，但一样的 `font-size`，在不同字体里的计算高度却是不一样的（需要引入 `line-box` 的概念来解释）。

![](https://upload-images.jianshu.io/upload_images/16527477-f8acd02fa0342f1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

另外，内联元素的高度并不影响其父元素的高度，如下：

![](https://upload-images.jianshu.io/upload_images/16527477-1d879de5f6b2bc0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是，对于可替换内联元素、inline-block 元素和 blockified 内联元素，`padding`、`margin` 和 `border` 会增大 `height`，因此会影响 `content-area` 和 `line-box` 的高度，进而影响其父元素的高度。（如 `img`，给 `img` 标签设置宽高是可以影响图片大小的）

有关内联元素高度的参考：[深入理解 CSS：字体度量、line-height 和 vertical-align](https://zhuanlan.zhihu.com/p/25808995)

继续看上图例子，当设置了 `span` 的 `border`、`padding`、`margin` 时，均会影响其父元素中其他元素所占**宽度**。

**Tips**：如果 `span` 元素中只有一个单词，中间没有空格，那么其内容会一直超出屏幕不换行（或者形成横向滑动条），除非添加属性：`word-break: break-all;`（中文网站使用较多）或者 `word-break: break-word;`。

# 块级元素的宽高

### （1）块级元素中只有内联元素

这里需要先引入文档流的概念。

**文档流(normal flow)**，直译为普通流，是指文档内元素的流动方向，块级元素因为默认**宽度**是其容器的 100%，所以它的文档流是从上至下；内联元素无法设置宽高，它的边框是紧贴内容的（注意可替代元素），所以它的文档流是从左至右的，如果超过浏览器宽度，则另起一行继续流。

1.  当 `div` 中只有一行 `span` 时，其默认**高度**由 `line-height` 决定，由上文可知，是由字体设计师决定的
2.  当 `div` 中有多行 `span` 时，这个**高度**会由 `line-height`、 `padding` 、`border` 以及内联元素的文档流综合决定

这里涉及到一个中文处理时常常遇到的问题：**文字左右对齐**。解决方案如下：

![](https://upload-images.jianshu.io/upload_images/16527477-8b8b5170c6184015.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### （2）块级元素中只有块级元素

如果一个 `div` 里有其他 子`div` ，那么这个 `div` 是由其内部子 `div` 的高度加上他们的 `margin` 和 `border` 以及其自身的 `padding`构成。

块级元素的高度是很清晰的，但是这里也有一个常常遇到的 bug，即 [**margin 合并**](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)
。

![](https://upload-images.jianshu.io/upload_images/16527477-b44cc6b0b271f89b.gif?imageMogr2/auto-orient/strip)

![](https://upload-images.jianshu.io/upload_images/16527477-2d07989b93a6aa64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，当父元素没有东西挡住儿子的 `margin` 时，那么子元素的 `margin` 就会和父元素的 `margin` 合并（仅上下合并，左右不影响）。
经验证，这里的**没有东西挡住**，不仅仅是 `border`，还有 `padding`。

其实，就是因为父元素不是 BFC，让它变成 BFC 即可避免。

解决方案：

-   给父元素加 `border`
-   给父元素加 `padding`
-   给父元素加 `overflow: hidden;`

### （3）块级元素中既有内联元素也有块级元素

如果一个块级元素中既有内联元素也有块级元素，那么它的**高度由其内部文档流元素的高度总和决定**。注意这里是决定，不是相等。

# 一些小技巧

### （1）单行文本省略

![](https://upload-images.jianshu.io/upload_images/16527477-488b5e99d9f3e55e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### （2）多行文本省略

这里只用记住省略号的单词（ellipsis）就可以了，然后搜索代码。
注：这里只支持 webkit 内核的浏览器

![](https://upload-images.jianshu.io/upload_images/16527477-21220ff349a1215b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### （3）实现一个 1:1 的 div

注：这个 `div` 是任意比例可缩放的。

```
div {
  padding-top: 100%
}
```
