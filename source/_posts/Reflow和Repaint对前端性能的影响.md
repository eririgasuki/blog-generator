---
title: Reflow和Repaint对前端性能的影响
date: 2019-03-04 16:14:35
tags:
---

# Reflow和Repaint是什么？

*Repaint* 就是“重绘”，它会在你改变 DOM 元素的视觉效果时进行，改变布局时不会触发。比如，`opacity,background-color,visibility`和`outline`等都会触发，“重绘”的开销还是比较昂贵的，因为浏览器会在某一个DOM元素的视觉效果改变后去check这个DOM元素内的所有节点。

*Reflow* 就是“回流”，它的影响更大。它会在某一个DOM元素的位置发生改变后触发，而且它会重新计算所有元素的位置和在页面中的占有的面积，这样的话将会引起页面某一个部分甚至整个页面的重新渲染。改变某一个元素会影响它所有的子节点 (children)、祖先节点 (ancestors) 及兄弟节点(siblings)。

浏览器的对待页面是*先布局后渲染*,在PC端Reflow和Repaint对于性能的影响是微乎其微，但是在*移动端*这俩货简直就是*性能杀手*。

repaint和reflow是DOM操作影响性能的主要原因。一个节点触发了repaint，浏览器会检查DOM Tree中其他所有节点的显示方式；一个节点触发了reflow会导致它的祖先节点，后代节点以及在它之后的节点全部reflow。*reflow对性能的影响大于repaint*。

什么情况下会触发Reflow？

* 添加、删除或者改变DOM元素的可见性时：使用JS去改变DOM元素时会触发Reflow。
* 添加、删除或者改变CSS样式：直接改变CSS Style或者元素的class可能会影响布局，还有改变一个元素的宽度能够影响它所在的DOM节点中的所有元素，以及它周围的那些元素。
* CSS3 动画（`animation`）和过渡（`transition`）: 动画的每一frame都会触发Reflow。
* 使用`offsetWidth`和`offsetHeight`：这一点很特别，你读一个DOM的offsetWidth和offsetHeight属性同样会触发一下Reflow，因为这两个属性需要依赖一些元素去计算。
* 用户交互：用户可以通过`:hover`一下`<a>`链接，在input里面输入文字，拖动浏览器的大小，改变字体大小，更换样式表或者字体等都会触发reflow。

# 如何提高性能？

一些常用的提高性能的原则

<table>
    <thead>
        <tr>
            <th style="width: 24%">方面</th>
            <th>方法</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>布局</td>
            <td>不要用 inline style 或 table 布局，flexbox 布局也会给性能带来一些小困扰。inline style 会在 html下载完后进行一次额外的 Reflow，table布局的开销远比其他 DOM 元素的布局开销要大。flexbox 的 item 会在HTML 下载完成后改变尺寸。</td>
        </tr>
        <tr>
            <td>简写CSS</td>
            <td>尽量简写CSS，避免使用复杂的CSS选择器，使用 Unused CSS https://unused-css.com/）, uCSS（https:/github.com/oyvindeh/ucss）, gulp-uncss（https://github.com/ben-eb/gulp-uncss）可以有效的减少样式定义和文件的大小。</td>
        </tr>
        <tr>
            <td>优化DOM</td>
            <td>减少DOM的层级，减少DOM的数量，如果不需适配老浏览器，删掉一些无用的wrapper性质的DOM元素，总之越少好。</td>
        </tr>
        <tr>
            <td>慎改class</td>
            <td>在一个DOM树中，尽可能改那些没有特别多子元素DOM的class，子元素少的可以改，多的不推荐。</td>
        </tr>
        <tr>
            <td>避免复杂动画</td>
            <td>删掉复杂的动画，运用动画的元素尽量是position:absolute或position:fixed的，这样会让他们脱离文档流不去影响其他的元素。</td>
        </tr>
        <tr>
            <td>善用display:none</td>
            <td>display:none的元素不会引发Reflow和Repaint，可以在让这些元素在display之前进行一些诸如颜色、尺寸么的改变。</td>
        </tr>
        <tr>
            <td>批量更新元素</td>
            <td>将状态写入一个类中，用JQ直接给元素添加或删除这个类，而不是直接通过方法来修改元素样式状态。</td>
        </tr>
        <tr>
            <td>避免大量DOM互相影响</td>
            <td>比如Tabs这种场景，如果你点击一个Tab会显示它控制的区块，显示的那个区块会影响其他的区块，这样可能会起Reflow，因为它们的高度不一样，可以通过定个高度来优化这种场景。</td>
        </tr>
        <tr>
            <td>性能永远比酷炫重要</td>
            <td>记住一个原则，你网页的动画再牛逼，性能还是第一位的，如果每一帧移动1个像素会造成你的页面卡顿，那宁愿一帧移动10像素让动画的帧变得迟钝一些，也不要让页面的性能降下来。</td>
        </tr>
    </tbody>
</table>

[参考链接](https://juejin.im/entry/595b5c135188250d7767f7d3)