---
title: CSS深入浅出总结
date: 2019-01-18 16:50:45
tags:
---

# CSS 的学习思路

理解常用原理，熟练常用套路。

* 控制文档流：利用好默认的宽度和高度规则
* 控制 z-index：堆叠上下文和堆叠顺序
* 理解 BFC
* 理解 IFC
* 理解 Float 布局和 Flex 布局
* 理解响应式
* 掌握动态 REM 方案
* 掌握 icon 的使用
* 掌握 Bootstrap 的使用
* 其他套路收集

别人研究出来我直接用即可。

[animate.css](https://daneden.github.io/animate.css/)
[CSS tricks](https://css-tricks.com/)
[Codepen](https://codepen.io/pens/)
[Codrops](https://tympanus.net/codrops/)
[itmeo](https://itmeo.com/)

# 控制文档流：利用好默认的宽度和高度规则

* 文档流中内联元素默认从左到右排列，宽度不够则自动换行
* 文档流中块级元素从上到下排列，每个元素占一行
* float:left、position:absolute、position: fixed 可以使元素脱离文档流
* 块级元素的高度由其文档流中元素高度的总和确定
* 给 inline 元素设置宽高是没有任何效果的
* height 可以用 line-height 和 padding 来撑，这样写更灵活美观

两行文字「姓名」与「联系方式」两端对齐套路
```
.span{
    display:inline-block;
    width:4em;
    text-align:justify;
    overflow:hidden;
}
.span::after{
    content:"";
    display:inline-block;
    width:100%;
}
```
多行(3)文本超出部分变成省略号的套路
```
${
    overflow: hidden;  
    text-overflow: ellipsis;  
    display: -webkit-box;  
    -webkit-line-clamp: 3;  
    -webkit-box-orient: vertical; 
}
```

# 控制 z-index：堆叠上下文和堆叠顺序

[堆叠上下文](https://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)

堆叠顺序
* z-index: -
* background
* border
* 块级
* 浮动
* 内联
* z-index: 0
* z-index: +
* 如果是兄弟元素重叠，那么后面的盖在前面的身上。

堆叠作用域,跟 BFC 一样,如下属性会触发堆叠上下文

* 根元素 (HTML),
* z-index 值不为 "auto"的 绝对/相对定位，
* 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex，
* opacity 属性值小于 1 的元素（参考 the specification for opacity），
* transform 属性值不为 "none"的元素，
* mix-blend-mode 属性值不为 "normal"的元素，
* filter值不为“none”的元素，
* perspective值不为“none”的元素，
* isolation 属性被设置为 "isolate"的元素，
* position: fixed
* 在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值
* -webkit-overflow-scrolling 属性被设置 "touch"的元素

# 理解 BFC

一个块格式化上下文（block formatting context） 是Web页面的可视化CSS渲染出的一部分。它是块级盒布局出现的区域，也是浮动层元素进行交互的区域。

一个块格式化上下文由以下之一创建：
* 根元素或其它包含它的元素
* 浮动元素 (元素的 float 不是 none)
* 绝对定位元素 (元素具有 position 为 absolute 或 fixed)
* 内联块 (元素具有 display: inline-block)
* 表格单元格 (元素具有 display: table-cell，HTML表格单元格默认属性)
* 表格标题 (元素具有 display: table-caption, HTML表格标题默认属性)
* 具有overflow 且值不是 visible 的块元素，
* display: flow-root
* column-span: all 应当总是会创建一个新的格式化上下文，即便具有 column-span: all 的元素并不被包裹在一个多列容器中。
一个块格式化上下文包括创建它的元素内部所有内容，除了被包含于创建新的块级格式化上下文的后代元素内的元素。

BFC的作用：
1. 父元素管子元素：用 BFC 包住浮动元素。
2. 兄弟元素划清界限：用 float + div 做左右自适应布局。

避免其他bug的触发BFC套路：`display: flow-root; /*触发BFC*/`

# 理解 IFC

IFC(inline formatting context)规则：在行内格式化上下文中，框(boxes)一个接一个地水平排列，起点是包含块的顶部。水平方向上的 margin，border 和 padding在框之间得到保留。框在垂直方向上可以以不同的方式对齐：它们的顶部或底部对齐，或根据其中文字的基线对齐。包含那些框的长方形区域，会形成一行，叫做行框。

[《深入理解 CSS：字体度量、line-height 和 vertical-align》](https://zhuanlan.zhihu.com/p/25808995)

IFC应用：
* 图片下面有空隙
    vertical-align: top
    img{display: block;}
    font-size: 0 不建议，bug多
* inline-block 元素对不齐 ： 用 flex 或 float
* inline-block 有空隙 ： flex 或 float

# 理解 Float 布局和 Flex 布局

* 不到万不得已，不要写死 width 和 height
* 尽量用高级语法，如 calc、flex
* 如果是 IE，就全部写死

[Flex 布局教程](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
如果宽度不够，可以用 margin: 0 -4px;

float布局父元素加clearfix
```
.clearfix:after{
    content: '';
    display: block;
    clear: both;
}
.clearfix{
    zoom: 1;
}
```

# 理解响应式

手机端页面的做法:
* media query
* 先确保设计图，后写代码
* 隐藏元素
* 手机端要加一个 meta`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`

手机端的交互方式不一样
* 没有 hover
* 有 touch 事件
* 没有 resize
* 没有滚动条

# 掌握动态 REM 方案

1. 手机端方案的特点
    * 所有手机显示的界面都是一样的，只是大小不同
    * 1 rem == html font-size == viewport width

2. 使用 JS 动态调整 REM
    ```
     <script>
         var pageWidth = window.innerWidth
         document.write('<style>html{font-size:'+pageWidth+'px;}</style>')
     </script>
    ```
3. REM 可以与其他单位同时存在

4. 在 SCSS 里使用 PX2REM
    ```
    mkdir ~/Desktop/scss-demo
    cd ~/Desktop/scss-demo
    mkdir scss css
    touch scss/style.scss
    start scss/style.scss
    node-sass -wr scss -o css
    ```
    编辑 scss 文件就会自动得到 css 文件
    在 scss 文件里添加
    ```
    @function px( $px ){
      @return $px/$designWidth*10 + rem;
    }
    $designWidth : 640; // 640 是设计稿的宽度，你要根据设计稿的宽度填写。
    .child{
      width: px(320);
      height: px(160);
      margin: px(40) px(40);
      border: 1px solid red;
      float: left;
      font-size: 1.2em;
    }
    ```
    即可实现 px 自动变 rem

# 掌握 icon 的使用

[使用SVG](https://www.iconfont.cn/)
第一步：拷贝项目下面生成的symbol代码：
```
//at.alicdn.com/t/font_8d5l8fzk5b87iudi.js
```
第二步：加入通用css代码（引入一次就行）：
```
<style type="text/css">
    .icon {
       width: 1em; height: 1em;
       vertical-align: -0.15em;
       fill: currentColor;
       overflow: hidden;
    }
</style>
```
第三步：挑选相应图标并获取类名，应用于页面：
```
<svg class="icon" aria-hidden="true">
    <use xlink:href="#icon-xxx"></use>
</svg>
```

# 掌握 Bootstrap 的使用

Bootstrap 的使用方法就是复制粘贴

# 其他套路收集

磨砂效果思路：底部色彩用background-color，白噪声采用png图片作为background-image。优化一下，采用base64方式设background-image。
```
body {
  background-color: #000000;
  background-image: url(data:image/png;base64,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); 
}

```
[参考资料](https://segmentfault.com/q/1010000007793299)


磨砂玻璃效果:
```
body, main::before { 
    background: url("tiger.jpg") 0 / cover fixed; 
} 
main { 
    position: relative; 
    background: hsla(0,0%,100%,.3); 
    overflow: hidden; 
} 
main::before { 
    content: ''; 
    position: absolute; 
    top: 0; right: 0; bottom: 0; left: 0; 
    filter: blur(20px); 
    margin: -30px; 
}
```
[参考资料](https://www.w3cplus.com/css3/css-secrets/frosted-glass-effect.html)


