![](https://upload-images.jianshu.io/upload_images/16527477-1f649c1c3bd9bf45.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 目录

- [目录](#目录)
- [HTTP 简述](#http-简述)
- [URL](#url)
- [HTTP 请求](#http-请求)
  - [格式](#格式)
  - [GET 请求](#get-请求)
      - [curl 命令](#curl-命令)
  - [POST 请求](#post-请求)
  - [GET 和 POST 的区别](#get-和-post-的区别)
  - [用 Chrome 发请求](#用-chrome-发请求)
- [HTTP 响应](#http-响应)
  - [格式](#格式-1)
  - [用 Chrome 查看响应](#用-chrome-查看响应)
- [URI 和 URL 的区别](#uri-和-url-的区别)
- [总结](#总结)

# HTTP 简述

> HTTP 协议，即**超文本传输协议**（HyperText Transfer Protocol），是一种用于分布式、协作式和超媒体信息系统的应用层协议。HTTP 是万维网的数据通信的基础。

HTTP 本身是[Tim Berners-Lee](https://baike.baidu.com/item/%E8%92%82%E5%A7%86%C2%B7%E4%BC%AF%E7%BA%B3%E6%96%AF%C2%B7%E6%9D%8E?fromtitle=Tim+Berners-Lee&fromid=1836386)发明的[万维网](https://baike.baidu.com/item/www/109924?fromtitle=%E4%B8%87%E7%BB%B4%E7%BD%91&fromid=215515)中重要三个概念之一，即 URL，HTTP，HTML。
其中，

-   URL，俗称网址
-   HTTP，两个电脑之间传输内容的协议
-   HTML，超级文本，主要用来做页面跳转

通俗地讲，URL 的作用是能让你访问一个页面，HTTP 的作用是让你能下载这个页面，HTML 的作用是让你能看懂这个页面。
通常，由 HTTP 客户端发起一个请求，创建一个到服务器指定端口（默认是 80 端口）的 TCP 连接。HTTP 服务器则在那个端口监听客户端的请求。一旦收到请求，服务器会向客户端返回一个状态，比如"HTTP/1.1 200 OK"，以及返回的内容，如请求的文件、错误消息、或者其它信息。

# URL

了解 HTTP 之前，需要了解下 [URL](https://baike.baidu.com/item/url/110640?fr=aladdin)。

> url 是统一资源定位符，是对可以从互联网上得到的资源的位置和访问方法的一种简洁的表示，是互联网上标准资源的地址。

这里主要了解它的结构。比如：

```
https://www.baidu.com/s?wd=Jay#6
```

-   `https://`： 超文本传输安全协议
-   `www.baidu.com`： 域名
-   `/s`： 路径
-   `?wd=Jay`： 查询参数
-   `#6`： 锚点

# HTTP 请求

HTTP/1.1 协议中定义了多种请求方法，包括 GET，POST，GET，PUT，PATCH， DELETE，HEAD，OPTIONS 等，这里只介绍最常见的 GET 和 POST 请求。

## 格式

> 请求方法 路径 协议/版本 <br>
> Key1: value1<br>
> Key2: value2<br>
> Key3: value3<br>
> Content-Length: n<br>
> Content-Type: application/x-www-form-urlencoded<br>
> Host: www.example.com<br>
> User-Agent: curl/7.56.0
>
> 要上传的数据

-   第一行是第一部分：**请求行**<br>
    请求方法就是前面提到的那几种，常用 GET，POST（思考它们的区别）。这里的路径包括「查询参数」，但不包括「锚点」。如果你没有写路径，那么路径默认为 / 。
-   第二行直到回车是第二部分：**请求报头**<br>
    请求报头包含客户端向服务器传递请求的附加信息和客户端自身的信息，由关键字/值对组成，每行一对，关键字和值用英文冒号 `:` 分隔。
    常见的请求报头有: Accept, Accept-Charset, Accept-Encoding, Accept-Language, Content-Type, Authorization, Cookie, User-Agent 等。
-   第三部分永远都是一个回车（\n）
-   回车后的内容是第四部分：**请求正文**<br>
    当使用 POST、PUT 等方法时，通常需要客户端向服务器传递数据。这些数据就储存在请求正文中。请求正文可以承载多个请求参数的数据，包含回车符、换行符和请求数据，并不是所有请求都具有请求数据（也就是说第四部分可以为空）。
    在请求包头中有一些与请求正文相关的信息，例如：现在的 Web 应用通常采用 Rest 架构，请求的数据格式一般为 json。这时就需要设置 `Content-Type: application/json`。

## GET 请求

这里先介绍一个工具 curl。

#### curl 命令

> curl 是一个利用 URL 规则在命令行下工作的文件传输工具，可以说是一款很强大的 http 命令行工具。它支持文件的上传和下载，是综合传输工具，但按传统，习惯称 url 为下载工具。

它可以很直观地将你要访问的网站的请求和响应表示出来，这里我们以访问国内最大同性交友网站 [bilibili](https://www.bilibili.com) 为例。

```
curl -s -v -H "key: value" -- "https://www.bilibili.com"
```

如上，

-   `-s`：不要显示进度
-   `-v`：显示一次的 http 请求的通信过程，去掉的话只返回 HTML 文档，没有请求和响应
-   `-H`：添加后面的内容到请求头，来自定义 Header，里面的内容任意，比如：`"Referer: www.example.com"`
-   这是一个 GET 请求

我们将会得到如下结果。先是显示内容的头部，HTML 文件太大没截全图：

![](http://upload-images.jianshu.io/upload_images/16527477-b9a1dc26507c1810.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

显示内容的尾部：

![](https://upload-images.jianshu.io/upload_images/16527477-8cf11251786184bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## POST 请求

当需要发送表单数据到服务器时，可以用 POST 请求。

```
curl -X POST -d "title=xxx&text=yyy" www.example.com/add
```

-   `-X`：指定发送数据的方式，这里是 POST
-   curl 默认以 `application/x-www-form-urlencoded` 作为 Content-Type 来发送数据

那么会相应地返回结果如下：

```
POST / HTTP/1.1
Host: www.examle.com/add
User-Agent: curl/7.56.0
Accept: */*
Content-Length: 18
Content-Type: application/x-www-form-urlencoded

title=xxx&text=yyy
```

## GET 和 POST 的区别

get 和 post 虽然本质都是 tcp/ip，但两者除了在 http 层面外，在 tcp/ip 层面也有区别。

get 会产生一个 tcp 数据包，post 两个。具体就是：

-   get 请求时，浏览器会把 headers 和 data 一起发送出去，服务器响应 200（返回数据）
-   post 请求时，浏览器先发送 headers，服务器响应 100 continue， 浏览器再发送 data，服务器响应 200（返回数据）

## 用 Chrome 发请求

1.  按 F12 进入开发者模式
1.  打开 Network
1.  地址栏输入网址
1.  在 Network 点击，查看 request，点击「view source」
1.  如果有请求的第四部分，那么在 FormData 或 Payload 里面可以看到

![](http://upload-images.jianshu.io/upload_images/16527477-399c8f1c50b5cefc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# HTTP 响应

在上面访问 bilibili 的例子中，显示末尾的一部分就是服务器的响应：

![GET响应](http://upload-images.jianshu.io/upload_images/16527477-10be7b7151807c99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再加上前面很长的那个 HTML 文件，一起组成了 HTTP 响应的结构。

## 格式

> 协议/版本号 状态码 状态解释<br>
> Key1: value1<br>
> Key2: value2<br>
> Content-Length: n<br>
> Content-Type: text/html<br>
>
> 要下载的内容

可以看到，响应的格式和请求格式基本一致。
状态码是服务器对浏览器说的话，可以粗暴地如下来记忆：

> 1xx 表示 server 说：我知道了，你继续<br>
> 2xx 表示成功<br>
> 3xx 表示 server 说：滚吧<br>
> 4xx 表示 server 说：你丫错了<br>
> 5xx 表示 server 说：好吧，我错了

一些常见的状态码：

```
200 OK                        //客户端请求成功
400 Bad Request               //客户端请求有语法错误，不能被服务器所理解
401 Unauthorized              //请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用
403 Forbidden                 //服务器收到请求，但是拒绝提供服务
404 Not Found                 //请求资源不存在，eg：输入了错误的URL
500 Internal Server Error     //服务器发生不可预期的错误
502 Bad Gateway	              //作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应
503 Server Unavailable        //服务器当前不能处理客户端的请求，一段时间后可能恢复正常
```

## 用 Chrome 查看响应

1.  按 F12 进入开发者模式
1.  打开 Network
1.  输入网址
1.  选中第一个响应
1.  查看 Response Headers，点击「view source」
1.  查看 Response 或者 Preview，你会看到响应的第 4 部分

![](http://upload-images.jianshu.io/upload_images/16527477-88c4f86df967dfb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Response](http://upload-images.jianshu.io/upload_images/16527477-d54887e1944d9104.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# URI 和 URL 的区别

小插曲，一图盖之：
![URI & URL](http://upload-images.jianshu.io/upload_images/16527477-547e6babb9875129.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 总结

HTTP 的知识对前端开发非常重要，这里只是浅谈了一下 HTTP 的格式、分类和一些状态码，等以后学习了 HTTP 更深入的知识再来填坑。
