![](https://upload-images.jianshu.io/upload_images/16527477-2659c279f42a3369.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 目录

- [目录](#目录)
- [概述](#概述)
- [需要掌握的标签](#需要掌握的标签)
  - [<a\>（HTTP GET请求）](#ahttp-get请求)
      - [1.target属性](#1target属性)
      - [2.download属性](#2download属性)
      - [3.href属性](#3href属性)
  - [<form\>（HTTP POST请求）](#formhttp-post请求)
      - [1.method属性](#1method属性)
      - [2.target属性](#2target属性)
  - [<input\>](#input)
      - [1.type="button" 和 type="submit"区别](#1typebutton-和-typesubmit区别)
      - [2.type="checkbox"](#2typecheckbox)
      - [3.type="radio"](#3typeradio)
  - [<table\>](#table)
  - [<meta\>](#meta)
  - [空标签](#空标签)
  - [可替换标签](#可替换标签)
- [语义化的理解](#语义化的理解)
- [总结](#总结)
      
# 概述

[HMTL](https://developer.mozilla.org/zh-CN/docs/learn/HTML/Introduction_to_HTML)（HyperText Markup Language）是一种相当简单的、由不同元素组成的标记语言，它可以应用于文本片段，使文本在文档中具有不同的含义，将文档结构化为逻辑块，并且可以将图片，影像等内容嵌入到页面中。
随着2014年10月29日HTML5的标准规范发布以来，业界对语义化编程的诉求越来越高，本文会在谈到一些需要着重掌握的标签属性同时，谈到对语义化的理解。HTML5 的 DOCTYPE 较之前有了大幅度简化，`<!DOCTYPE html>` 即可。

# 需要掌握的标签
这里先插入一个在如今开发环境中不常用的 `<iframe>` 元素来辅助我们的学习。它的形式如下：
```html
<iframe src="https://www.example.com" name="xxx"></iframe>
```
关于它的优缺点，这里不谈。<iframe\>还有一些骚操作，详见[知乎](https://www.zhihu.com/question/20653055)。

## <a\>（HTTP GET请求）
HTML [<a\>](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a) 元素（或称锚元素）可以创建通向其他网页、文件、同一页面内的位置、电子邮件地址或任何其他 URL 的超链接。在跳转页面的时候，`<a>` 用于发送 HTTP GET 请求。它是 HTML 中最短也最常用的标签之一。
#### 1.target属性
该属性指定在何处显示链接的资源。 取值为标签（tab），窗口（window），或框架（iframe）等浏览上下文的名称或其他关键词。以下关键字具有特殊的意义:
*  _self: 当前页面加载，即当前的响应到同一HTML 4 frame（或HTML5浏览上下文）。此值是默认的，如果没有指定属性的话。
*  _blank: 新窗口打开，即到一个新的未命名的HTML4窗口或HTML5浏览器上下文
*  _parent: 加载响应到当前框架的HTML4父框架或当前的HTML5浏览上下文的父浏览上下文。如果没有parent框架或者浏览上下文，此选项的行为方式与 _self 相同。
*  _top: IHTML4中：加载的响应成完整的，原来的窗口，取消所有其它frame。 HTML5中：加载响应进入顶层浏览上下文（即，浏览上下文，它是当前的一个的祖先，并且没有parent）。如果没有parent框架或者浏览上下文，此选项的行为方式相同_self。

结合上面的 `<iframe>` 元素，我们可以了解 target 属性会具体跳转到哪里。
这里先创造一种场景。假设在当前目录下，有 index.html index2.html index3.html，index.html中有 `<iframe src="./index2.html"></iframe>`，index2.html中有 `<iframe src="./index3.html"></iframe>`，而如下的代码均放在index3.html中。
```html
<a href="https://www.example.com" target="_self">自己</a>
<a href="https://www.example.com" target="_blank">新开页</a>
<a href="https://www.example.com" target="_parent">爹妈</a>
<a href="https://www.example.com" target="_top">祖宗</a>
```
如字面意思，_self会跳转到index3.html中打开，_blank无论何时何地都在新页面打开，_parent会跳转到index2.html中打开，_top则会跳转到顶层页面index.html中打开。

#### 2.download属性
```html
<a href="https://www.example.com" download>下载</a>
```
此属性指示浏览器下载 URL 而不是导航到它，因此将提示用户将其保存为本地文件。点击页面中的该“下载”标签，会直接下载。
另外的一种做法是将HTTP请求的 `content-type` 写成 `application/octet-stream`，但如果 `content-type` 是 `text/html`，则需要用download属性强制下载。
#### 3.href属性
href 包含超链接指向的 URL 或 URL 片段。
有以下几种情况需要注意：
-  无协议绝对地址：`href="//example.com"` => 会打开 http://www.example.com
-  相对地址：`href="xxx.html"` => 会打开 /xxx.html，是一个相对路径
-  只写锚点：`href="#abc"` => 页面内跳转，直接加到路径结尾，不会发起HTTP请求
-  写查询参数：`href="?name=frank"` => 跳转到 /?name=frank，并发起一个GET请求
-  伪协议：`href="javascript: alert(1)"` => 会直接执行。
一个奇葩需求：`href="javascript:;"` => 作用：建立一个不需要跳转的链接。
而直接用 `href="#"` 会跳转到页面顶部，或者 `href=""` 会刷新页面并发起一个GET请求

## <form\>（HTTP POST请求）
HTML [<form\>](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/form) 元素 表示了文档中的一个区域，这个区域包含有交互控制元件，用来向 web 服务器提交信息。
####  1.method属性
下面是一个用于提交用户名和密码的 form，常用于登录网址。
```html
<form action="users" method="post" target="_blank">
  <input type="text" name="username">
  <input type="password" name="password">
  <input type="submit" value="提交">
</form>
```
首先，一个表单如果没有提交按钮，则无法提交表单，除非用js。
假设用户输入的 username 是 Tom，password 是 123456，点击提交按钮后，会发起一个POST请求，并且在POST请求的第四部分，name和password会以 `Tom&123456` 的形式发送出去，其`Content-Type: application/x-www-form-urlencoded`。而基于这个 content-type，所有非英文的内容都会被转义为 UTF-8。
`<form>` 的 method 属性默认情况下为 get，即发起一个GET请求，但这并没有什么用，直接用 GET 请求相当于在网址末尾加上 `users?name=Tom&password=123456`。
POST 请求添加查询参数的方式是加到 action 里：比如 `action="users?z=123"`。
#### 2.target属性
`<form>` 标签的 target 属性和 `<a>` 标签完全一致。
## <input\>
HTML[<input\>](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input)元素用于为基于Web的表单创建交互式控件，以便接受来自用户的数据; 可以使用各种类型的输入数据和控件小部件，具体取决于设备和 [user agent](https://developer.mozilla.org/en-US/docs/Glossary/user_agent "user agent: A user agent is a computer program representing a person, for example, a browser in a Web context.")。
`<input>` 有非常多的 type，以下是几种介绍。
#### 1.type="button" 和 type="submit"区别
就视觉上而言，两者长得一样，那么它们有什么区别呢？

第一种情况，两者都没有：
```html
<form action="users" method="post" target="_blank">
  <input type="text" name="username">
  <input type="password" name="password">
  <button>提交</button>
</form>
```
注意到，这里的按钮是唯一的提交按钮，那么它会自动升级为 submit 按钮，即点击“提交”按钮，表单能发送成功。但如果此时 button 添加了属性 `type=button` 则会失败。

第二种情况，有 `type="button"`：
```html
<form action="users" method="post" target="_blank">
  <input type="text" name="username">
  <input type="password" name="password">
  <input type="button" value="提交">
</form>
```
这里的 button 只是普通按钮，并没有 submit 功能，因此无法提交表单。

第三种情况，有 `type="submit"`：
```html
<form action="users" method="post" target="_blank">
  <input type="text" name="username">
  <input type="password" name="password">
  <input type="submit" value="提交">
</form>
```
表单正常提交。
因此，有且仅有 submit 按钮存在时才能提交表单（注意 `<button>` 自动升级的情况）。

#### 2.type="checkbox"
表示复选框。必须使用 value 属性定义此控件被提交时的值。使用 checked 属性指示控件是否被选择。
```html
喜欢的水果：
<label><input type="checkbox" name="fruit" value="orange">橘子</input></label>
<label><input type="checkbox" name="fruit" value="apple" checked>苹果</input></label>
<label><input type="checkbox" name="fruit" value="banana">香蕉</input></label>
```
使用 `<label>` 标签将内容包起来，可以点击文字直接选中，否则只能点方框。
必须有 name 才能被提交到服务器上，如果三个全部选中，那么 POST 请求的第四部分是 `fruit=orange&fruit=apple&fruit=banana`。

#### 3.type="radio"
表示单选按钮。必须使用 value 属性定义此控件被提交时的值。使用 checked 必须指示控件是否缺省被选择。在同一个“单选按钮组”中，所有单选按钮的 name 属性使用同一个值； 一个单选按钮组中是，同一时间只有一个单选按钮可以被选择。
```html
你女朋友问你：爱不爱我？
<label><input type="radio" name="loveme" value="yes" checked>YES</input></label>
<label><input type="radio" name="loveme" value="yes">YES</input></label>
```
使用一样的 name 即可只选中一个。

`<input>` 标签中还有很多 HTML5 中用到的 type，以后会逐渐补齐。
## <table\>
HTML的 `<table>` 元素表示表格数据 — 即通过二维数据表表示的信息。

表格的内容按照这个顺序：

*   一个可选的 [`<caption>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/caption "HTML <caption> 元素 (or HTML 表格标题元素) 展示一个表格的标题， 它常常作为 <table> 的第一个子元素出现，同时显示在表格内容的最前面，但是，它同样可以被CSS样式化，所以，它同样可以出现在任何一个一个相对于表格的做任意位置。") 元素
*   零个或多个的 [`<colgroup>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/colgroup "HTML 中的 表格列组（Column Group <colgroup>） 标签用来定义表中的一组列表。") 元素
*   一个可选的 [`<thead>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/thead "HTML的<thead>元素定义了一组定义表格的列头的行。") 元素
*   一个可选的 [`<tfoot>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/tfoot "HTML 元素<tfoot>  定义了一组表格中各列的汇总行。") 元素（tfoot元素出现在tbody或tr元素前后都可以。在HTML4中，它只能出现在这些元素之前）
*   零个或多个 [`<tbody>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/tbody "这个 HTML 标签  元素在一个  元素中可以出现一个或者更多。") 元素
*   一个或多个 [`<tr>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/tr "HTML <tr> 元素定义表格中的行。 Those can be a mix of <td> and <th> elements.") 元素
```html
<table>
        <caption>成绩表</caption>
        <colgroup>
            <col class="column1">
            <col class="columns2plus3" span="2">
        </colgroup>
        <thead>
            <tr>
                <th></th>
                <th>Name</th>
                <th>Class</th>
                <th>Score</th>
            </tr>
        </thead>
        <tfoot>
            <tr>
                <th>Average</th>
                <td></td>
                <td></td>
                <td>80</td>
                <tr>
                    <th>Total</th>
                    <td></td>
                    <td></td>
                    <td>240</td>
                </tr>
        </tfoot>
        <tbody>
            <tr>
                <td></td>
                <td>Shawn</td>
                <td>2</td>
                <td>100</td>
            </tr>
            <tr>
                <td></td>
                <td>Tom</td>
                <td>1</td>
                <td>60</td>
            </tr>
            <tr>
                <td></td>
                <td>Frank</td>
                <td>3</td>
                <td>80</td>
            </tr>

        </tbody>
    </table>
```
如图是一个结构比较完整的表格，加上一点 CSS 呈现如下：

![](https://upload-images.jianshu.io/upload_images/16527477-76dac670634ee502.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中的灰色部分是选中 `colgroup` 中的 columns2plus3 来实现的。我们注意到代码中的 `<tfoot>`在 `<tbody>` 上面，但是不影响结果的呈现。

## <meta\>
HTML[`<meta>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta)元素表示那些不能由其它HTML元相关元素 ([`<base>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/base "HTML <base> 元素 指定用于一个文档中包含的所有相对URL的基本URL。一份中只能有一个<base>元素。"), [`<link>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link "HTML 中<link>元素规定了外部资源与当前文档的关系。 这个元素可用来为导航定义一个关系框架。这个元素最常于链接样式表。"), [`<script>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/script "HTML <script> 元素用于嵌入或引用可执行脚本。"), [`<style>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/style "HTML的<style>元素包含文档的样式信息或者文档的部分内容。默认情况下，该标签的样式信息通常是CSS的格式。") 或 [`<title>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/title "HTML <title> 元素 定义文档的标题，显示在浏览器的标题栏或标签页上。它只可以包含文本，若是包含有标签，则包含的任何标签都不会被解释。")) 之一表示的任何元数据信息.
<meta>有非常多的属性，这里我们只讲 `name="viewport"`，它提供有关视口初始大小的提示，仅供移动设备使用。目前研究还不深，这里只提供文档。
-  [MDN viewport](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@viewport)
-  [github HEAD](https://github.com/joshbuchea/HEAD)
-  [移动前端开发之viewport的深入理解](https://www.cnblogs.com/2050/p/3877280.html)

## 空标签
一个[空元素](https://developer.mozilla.org/zh-CN/docs/Glossary/%E7%A9%BA%E5%85%83%E7%B4%A0)可能是 HTML，SVG，或者 MathML 里的一个不可能存在子节点（例如内嵌的元素或者元素内的文本）的元素。

在 HTML 中有以下这些空元素：

*   [`<area>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/area "HTML <area> 元素 在图片上定义一个热点区域，可以关联一个超链接。<area>元素仅在<map>元素内部使用。")
*   [`<base>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/base "HTML <base> 元素 指定用于一个文档中包含的所有相对URL的基本URL。一份中只能有一个<base>元素。")
*   [`<br>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/br "HTML <br> 元素在文本中生成一个换行（回车）符号。此元素在写诗和地址时很有用，这些地方的换行都非常重要。")
*   [`<col>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/col "HTML <col> 元素 定义表格中的列，并用于定义所有公共单元格上的公共语义。它通常位于<colgroup>元素内。")
*   [`<colgroup>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/colgroup "HTML 中的 表格列组（Column Group <colgroup>） 标签用来定义表中的一组列表。") when the [`span`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/colgroup#attr-span) is present
*   [`<command>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/command "command元素用来表示一个用户可以调用的命令.")
*   [`<embed>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/embed "HTML <embed> 元素将外部内容嵌入文档中的指定位置。此内容由外部应用程序或其他交互式内容源（如浏览器插件）提供。")
*   [`<hr>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/hr "HTML <hr> 元素表示段落级元素之间的主题转换（例如，一个故事中的场景的改变，或一个章节的主题的改变）。在HTML的早期版本中，它是一个水平线。现在它仍能在可视化浏览器中表现为水平线，但目前被定义为语义上的，而不是表现层面上。")
*   [`<img>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img "HTML Image 元素（ <img> ）代表文档中的一个图像。")
*   [`<input>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input "HTML <input> 元素用于为基于Web的表单创建交互式控件，以便接受来自用户的数据。")
*   [`<keygen>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/keygen "HTML <keygen> 元素是为了方便生成密钥材料和提交作为 HTML form 的一部分的公钥.这种机制被用于设计基于 Web 的证书管理系统。按照预想，<keygen> 元素将用于 HTML 表单与其他的所需信息一起构造一个证书请求，该处理的结果将是一个带有签名的证书。")
*   [`<link>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link "HTML 中<link>元素规定了外部资源与当前文档的关系。 这个元素可用来为导航定义一个关系框架。这个元素最常于链接样式表。")
*   [`<meta>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta "HTML <meta> 元素表示那些不能由其它HTML元相关元素 (<base>, <link>, <script>, <style> 或 <title>) 之一表示的任何元数据信息.")
*   [`<param>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/param "HTML <param>元素为<object>元素定义参数")
*   [`<source>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/source "HTML <source> 元素为 <picture>, <audio> 或者 <video> 元素指定多个媒体资源。这是一个空元素。它通常用于以不同浏览器支持的多种格式提供相同的媒体内容。 It is commonly used to serve the same media content in multiple formats supported by different browsers.")
*   [`<track>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/track "HTML <track> 元素 被当作媒体元素—<audio> 和 <video>的子元素来使用。它允许指定计时字幕（或者基于事件的数据），例如自动处理字幕。")
*   [`<wbr>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/wbr "HTML <wbr> 元素  — 一个文本中的位置，其中浏览器可以选择来换行，虽然它的换行规则可能不会在这里换行。")
## 可替换标签
CSS 里，[可替换元素](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Replaced_element)的展现不是由 CSS 来控制的。这些元素是一类外观渲染独立于 CSS 的 外部对象。 简单来说，它们的内容不受当前文档的样式的影响。CSS 可以影响可替换元素的位置，但不会影响到可替换元素自身的内容。某些可替换元素，例如 [`<iframe>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe "HTML内联框架元素 `<iframe>` 表示嵌套的浏览上下文，有效地将另一个 HTML 页面嵌入到当前页面中。") 元素，可能具有自己的样式表，但它们不会继承父文档的样式。
CSS 能对可替换元素产生的唯一影响在于，部分属性支持控制元素内容在其框中的位置或定位方式。

典型的可替换元素有：
*   [`<iframe>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe "HTML内联框架元素 <iframe> 表示嵌套的浏览上下文，有效地将另一个HTML页面嵌入到当前页面中。")
*   [`<video>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/video "HTML <video> 元素 用于在HTML或者XHTML文档中嵌入视频内容。")
*   [`<embed>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/embed "HTML <embed> 元素将外部内容嵌入文档中的指定位置。此内容由外部应用程序或其他交互式内容源（如浏览器插件）提供。")
*   [`<img>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img "HTML Image 元素（ <img> ）代表文档中的一个图像。")

有些元素仅在特定情况下被作为可替换元素处理，例如：

*   [`<option>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/option "在web表单中,  HTML元素 <option>  用于定义在<select>,  <optgroup> 或<datalist> 元素中包含的项。<option> 可以在弹出窗口和 html 文档中的其他项目列表中表示菜单项。")
*   [`<audio>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/audio "HTML <audio> 元素用于在文档中表示音频内容。 <audio> 元素可以包含多个音频资源， 这些音频资源可以使用 src 属性或者<source> 元素来进行描述； 浏览器将会选择最合适的一个来使用。对于不支持<audio>元素的浏览器，<audio>元素也可以作为浏览器不识别的内容加入到文档中。")
*   [`<canvas>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/canvas "<canvas>元素可被用来通过脚本（通常是JavaScript）绘制图形。比如,它可以被用来绘制图形,制作图片集合,甚至用来实现动画效果。你可以(也应该)在元素标签内写入可提供替代的的代码内容，这些内容将会在在旧的、不支持<canvas>元素的浏览器或是禁用了JavaScript的浏览器内渲染并展现。")
*   [`<object>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/object "HTML <object> 元素（或者称作 HTML 嵌入对象元素）表示引入一个外部资源，这个资源可能是一张图片，一个嵌入的浏览上下文，亦或是一个插件所使用的资源。")
*   [`<applet>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/applet "HTML中的Applet元素(<applet>) 标志着包含了Java的applet。")


# 语义化的理解

结构化的标签，它在一个网页结构中，可以清楚的表示该部分在文档中的结构含义。

HTML5 关注的不仅是呈现出一个页面的效果，它更加关注如何构造一个结构化的代码内容优雅地页面。
HTML5 新增的结构化标签虽然本质上都是 `div` 标签，但它用语义化的命名方式使得一个HTML页面更加有结构和清晰，在日后如果遇到重构等问题也更加方便。

语义化标签，顾名思义就是从标签的名称就能看出这个标签的作用是什么，按照设计的网页结构使用合理的标签可以使代码清晰易懂，一定程度上也做好了页面的 SEO（搜索引擎优化）。

语义化标签列举：
-  `<article> ` 定义外部的内容，可以是一片新的文章
-  `<aside>` 定义article以外的内容，aside的内容可用作文章的侧栏
-  `<fig caption>` 定义figure元素的标题
-  `<figure>` 对元素进行组合，使用fig caption元素为元素组添加标题
-  `<footer>` 定义section或文档的页脚
-  `<header>` 定义文档的页眉
-  `<hgroup>` 用于对section或网页的标题进行组合，使用fig caption元素为元素组添加标题
-  `<nav>` 定义导航链接部分
-  `<section>` 定义文章中的节。比如章节、页眉、页脚或文章中的其他部分。
-  `<time>` 定义日期或时间

# 总结
以上大概就是我在学习HTML过程中对标签的梳理。里面涉及到有些内容，比如 meta viewport 在移动端的应用，还需要以后有相关项目经历才能深刻理解。以及一些HTML5 中的新标签，特别是 `canvas`，`video`，`audio` 等，会在以后的文章里详细呈现。
