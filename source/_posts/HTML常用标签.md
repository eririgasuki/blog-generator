---
title: HTML常用标签
date: 2018-12-01 20:22:56
tags:
---

# <head>部分

`<head>` 元素包含了所有的头部标签元素,常用的有`<meta>,<title>,<link>`

## <title>

`<title>` 标签定义了不同文档的标题。
`<title>` 在 HTML/XHTML 文档中是必须的。
`<title>` 元素:
* 定义了浏览器工具栏的标题
* 当网页添加到收藏夹时，显示在收藏夹中的标题
* 显示在搜索引擎结果页面的标题

## <link>

`<link>` 标签定义了文档与外部资源之间的关系。
`<link> `标签通常用于链接到样式表:
```
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```
除了链接到 **css** 文件之外，常用的还有 **"shortcut icon"为网页标题加图标** 和 **DNS预获取 dns-prefetch 提升页面载入速度**
```
<link rel="shortcut icon" href="图片地址" />
<link rel="icon" href="图片地址" type="image/gif" />
<link rel="dns-prefetch" href="//www.zhix.net">
<link rel="dns-prefetch" href="//api.share.zhix.net">
<link rel="dns-prefetch" href="//bdimg.share.zhix.net">
```

## <meta>

* meta标签描述了一些基本的元数据。
* `<meta>` 标签提供了元数据.元数据也不显示在页面上，但会被浏览器解析。
* META 元素通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据。
* 元数据可以使用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他Web服务。

`<meta>`标签可以做的事情非常多也非常复杂，例如SEO优化，移动设备响应式，浏览器内核控制等。
[常用meta整理](http://www.runoob.com/w3cnote/meta.html)

## <script>

`<script>`标签用于加载脚本文件，如： JavaScript。

# <body>部分

## body语义化

在CSS布局之前，写HTML的时候，首先会想到用到语义化标签对整体进行大致分割。

* 语义元素：一个语义元素能够清楚的描述其意义给浏览器和开发者。
* 语义化：其实简单说来就是让机器可以读懂内容。 

[如何理解语义化](https://www.zhihu.com/question/20455165)

常用的语义化标签有
* `<header>`通常用于头部导航
* `<nav>`用于定义页面的导航链接部分区域
* `<main>`通常用于页面的主体内容
* `<section>`定义文档中的节；部件
* `<article>`定义独立的内容，文章
* `<aside>`定义页面主区域内容之外的内容（比如侧边栏）。aside 标签的内容应与主区域的内容相关.
* `<figure>`独立的流内容（图像、图表、照片、代码等等）。
* `<figcaption> `标签定义 `<figure> `元素的标题.
* `<footer>`页脚通常包含文档的作者，著作权信息，链接的使用条款，联系信息等

## body常用标签

在语义化分割大致区域之后，除了`<div>,<span>,<h1~h6>,<p>,<img>`等标签之外，常用的标签有`<iframe>,<a>,<form>,<input>/<button>,<table>`等。

### iframe标签

* 内联框架元素 `<iframe>` 表示嵌套的浏览上下文，有效地将另一个HTML页面嵌入到当前页面中。
* HTML`<a> `元素  (或锚元素) 可以创建一个到其他网页、文件、同一页面内的位置、电子邮件地址或任何其他URL的超链接。

iframe标签原本的用法在现在看来是不合时宜的，但是它的其他功能却是不错的黑魔法。
[iframe黑魔法](https://www.zhihu.com/question/20653055)

### a标签

a标签常用属性：
* download：此属性指示浏览器下载URL而不是导航到它。 （也可以用Content-Type:application / octet-stream来实现下载）
* href：包含超链接指向的URL或URL片段。
   * href="#top" 或者 href="#" 链接返回到页面顶部。
   * href=""会刷新页面。
   * href="//qq.com"这样的无协议链接，浏览器会根据当前协议，补全无协议链接的协议
      * 如果用 file:// 协议浏览页面，就会访问到 file://qq.com，这是一个不存在的路径
      * 应该尽量不使用 file:// 协议预览网页，以免无协议链接出错
   * href="javascript:;"作为伪协议可以做到**点击之后不会发生任何变化的a标签**，也可以把`;`换成其他js代码执行。
* target：该属性指定在何处显示链接的资源。
   * _self: 当前页面加载
   * _blank: 新窗口打开
   * _parent: 加载响应到当前框架的父框架
   * _top: 加载响应进入顶层浏览上下文
   * xxx: 连接到name=xxx的iframe标签

### form标签

* HTML `<form>` 元素 表示了文档中的一个区域，这个区域包含有交互控制元件，用来向web服务器提交信息。
* HTML `<table>` 元素表示表格数据 — 即通过二维数据表表示的信息。

form标签常用属性：
* action：指定请求路径,这个值可以被 `<button>` 或者 `<input>` 元素中的 formaction 属性重载（覆盖）。
* method: 指定请求动词,浏览器使用这种 HTTP 方式来提交 form
   * post: 指的是 HTTP POST 方法 ; 表单数据会包含在表单体内然后发送给服务器.
   * get: 指的是 HTTP GET 方法; 表单数据会附加在 action 属性的URI中，并以 '?' 作为分隔符, 然后这样得到的 URI 再发送给服务器
* target: 同iframe

from标签通常要结合input标签来使用。而且成功提交有如下必要条件：
* form 标签里面有一个 input type=submit 的元素 || form 标签里面有一个 button 元素，button 的 type 属性为空
* 如果 input 不加 name，那么在表单提交时，input 的值就不会出现在请求里

HTML `<input>`元素用于为基于Web的表单创建交互式控件，以便接受来自用户的数据。
[input标签常用值和属性](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input)

### table标签

HTML的 `<table>` 元素表示表格数据 — 即通过二维数据表表示的信息。

table元素常用子标签：
* thead,tbody,tfoot DOM顺序不影响渲染顺序，始终是head>body>foot，如果不写浏览器会都默认为body 
* tr 即table row
* td 即table data
* colgroup 可以设置每列宽度