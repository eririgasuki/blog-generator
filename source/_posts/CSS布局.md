---
title: CSS布局
date: 2018-12-22 13:32:50
tags:
---

# 左右布局

两列布局一般是一边定宽一边自适应，下面采用左定宽右自适应举例。

div块级元素本身是上下垂直方向放置，通过float让块级div脱离文档流，向左浮动。但浮动后仍会占据文档流空间，需要让右边的div向右移动左div的宽度避免重叠。

```
.left{
    width:300px;
    float:left;
}
.right{
    marigin-left:300px;
}
```

***

根据BFC原理，将右DIV设置overflow不为visible，让左右DIV形成一个BFC区域，BFC区域内元素不与float box重叠。
```
.left{
    width:300px;
    float:left;
}
.right{
    overflow:hidden;
}
```

BFC即：Block Formatting Context
布局规则：
* 内部的盒子会在垂直方向，一个个地放置；
* 盒子垂直方向的距离由margin决定，属于同一个BFC的两个相邻Box的上下margin会发生重叠；
* 每个元素的左边，与包含的盒子的左边相接触，即使存在浮动也是如此；
* BFC的区域不会与float重叠；
* BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之也如此；
* 计算BFC的高度时，浮动元素也参与计算。

BFC特性：
* 阻止垂直外边距折叠
* 不会重叠浮动元素
* 可以包含浮动

产生BFC的元素：
* 根元素；
* float的属性不为none；
* position为absolute或fixed；
* display为inline-block，table-cell，table-caption，flex；
* overflow不为visible


# 左中右布局

左中右布局一般是指的双飞翼布局。

给主内容外套一层wrapper,再给两侧留白。
实现nav在左，article在中间自适应宽度，aside在右。

```
<div>
    <div class="wrapper">
        <article>main</ariticle>
    </div>
    <nav>left</nav>
    <aside>right</aside>
</div>
```

左浮动后，由于wrapper宽100%，nav和aside会显示在wrapper下方，这时通过负边距来给nav和aside提供位置。将nav左移100%屏幕距离，nav就会出现在屏幕最左，将aside左移aside自身宽度，aside就会出现在屏幕最右。
```
.wrapper{
    width:100%;
    float:left;
}
.main{
    margin: 0 100px;
    height:300px
}
.nav{
    width:100px;
    hight:300px;
    float:left;
    margin-left:-100%;
}
.aside{
    width:100px;
    hight:300px;
    float:left;
    margin-left:-100px;
}
```

# 居中

最简单基础的方法，缺点是要有宽高。

```
margin: auto;
```

***

绝对定位于父容器,子元素离父元素距50%,然后将子元素移动本身宽高的一半。

```
positon:absolute;
left:50%
margin-left:-0.5*width;
top:50%;
margin-top:-0.5*height;
```

***

利用relative相对定位实现居中。
```
div,ul,li{
    float-left;posi-relative;
}
ul{
    left:50%;
}
li{
    right:50%;
}
```
![center.png](https://i.loli.net/2018/12/22/5c1de0d2ace46.png)

***

给div包裹一个wrapper，让wrapper内容居中。将块级元素div变为内联块元素,即对照变为和内容相同的内联元素就可以居中了,参考了IFC原理
```
.wrapper{text-align:center}
div{display:inline-block}
```

***

利用table-cell或者flex居中
```
.father{
    display：table；
}
.child{
    display: table-cell;
    vertical-align: middle;//使子元素垂直居中
    text-align: center;//使子元素水平居中
}
```
```
display: flex;//flex布局
justify-content: center;//使子项目水平居中
align-items: center;//使子项目垂直居中
```

# 其他常用技巧

##  SEO优化

用h1包裹logo，优化SEO,用text-indent：-9999px隐藏文字。
```
<h1>
<a href=http://www.seo100.net/>SEO优化</a>
</h1>
```
```
h1,a{
    height: *;
    width: *;
    text-indent: -9999px;
    background-image: url(images/logo.gif);
    background-repeat: no-repeat;
    display: block;
    position: relative;
}
```

## 用于让同行内多列div保持等高

```
.father{
    overflow:hidden;
}
.child{
    margin-bottom:-10000px;
    padding-bottom:10000px;
}
```

## 通用兼容各浏览器消除inline-block的4px间距方法，设置父元素

```
.father{
    font-size: 0;
    letter-spacing: -3px;
}
```

## clearfix

父元素没高度的时候，子元素如果浮动脱离了文档流导致无法撑起父元素。
清除浮动方法：.clearfix类（参考bootstrap）

```
.cf:before,
.cf:after{
    content: " "; /* 让css伪元素能够显示 */
    display: block; /* 触发bfc的元素防止边距合并与穿透 */
    clear：both;
}
.cf{    
    *zoom: 1; /* 为旧IE准备的，触发haslayout */
}
```

其他clearfix类代码

```
clearfix::after
display: block
content: "."
height: 0
line-height: 0
clear: both
visibility: hidden
```

## 消除默认边距，消除不同浏览器带来的影响

```
*{
    padding: 0;
    margin: 0;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
}
```