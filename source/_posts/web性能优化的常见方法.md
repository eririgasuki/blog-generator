---
title: web性能优化的常见方法
date: 2019-04-08 14:10:56
tags:
---

# web性能优化思路
首先理解页面加载过程，然后从中考虑可能的解决方向。
1. 缓存
2. DNS查询  --- 减少DNS查询
3. 建立TCP连接 --- 连接复用（`keep-alive`）
4. 发送HTTP请求 --- 减小cookie --- cache-control
5. 接受响应 --- Etag 304 --- Gzip
6. 接受完成
7. Doctype（html/xml)
8. 逐行解析
9. 解析到标签
  * 在页面中渲染标签 （IE）
  * 等CSS解析完了再渲染标签 （chrome）
10. 解析到CSS
  * 下载CSS
  * 继续解析看还有没有CSS
  * 并行下载，串行解析（IE同时下载4个，chrome同时下载8个）

## 使用 CDN

## 使用 Cache-Control

## 使用 Etag

## 使用 Gzip

## 合并文件（CSS、JS、图片）

## 调整 CSS 和 JS 的位置
CSS放到head里，js放在body最底部。

## 压缩图片的工具

## 增加域名以并行下载资源

## 懒加载和预加载

最开始只加载首屏内容，然后看情况预加载后面的内容。通过js监听`onscroll`来决定记载哪些内容。

