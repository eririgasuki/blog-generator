---
title: JavaScript数据类型转换
date: 2018-12-31 14:07:37
tags:
---

# 类型转换

## 任意类型转字符串

1. Sting(x)
![String(x).jpg](https://i.loli.net/2018/12/31/5c29c91a8febb.jpg)
2. x.toString()
![toSting.jpg](https://i.loli.net/2018/12/31/5c29c91abfa91.jpg)
3. x + ''
![x+''.jpg](https://i.loli.net/2018/12/31/5c29c91aaa96a.jpg)

## 任意类型转数字

* Number(x)
* parseInt(x, 10) MDN
* parseFloat(x) MDN
* x - 0
* +x

## 任意类型转布尔

1. Boolean(x)
2. !!x

## 内存图
数据区分为 Stack（栈内存） 和 Heap（堆内存）
简单类型的数据直接存在 Stack 里
复杂类型的数据是把 Heap 地址存在 Stack 里

![stackHeap.png](https://i.loli.net/2018/12/31/5c29d488595df.png)

```
var a = 1
var b = a
b = 2
// a = 1
```
```
var a = {name: 'a'}
var b = a
b = {name: 'b'}
// a.name = 'a'
```
```
var a = {name: 'a'}
var b = a
b.name = 'b'
// a.name = 'b'
```
```
var a = {name: 'a'}
var b = a
b = null
// a = 'a'
```
浏览器是先从左到右分析变量，再从右往左计算。所以a.x和a在stack里的地址不同。
``` 
var a = {n:1};
var b = a;
var a.x = a = {n:2};

alert(a.x); // undefined
alert(b.x); // [object object]
```
![stackHeapTest.png](https://i.loli.net/2018/12/31/5c29d7a5cd541.png)

## 垃圾回收

清理没有被stack区引用的heap区对象,释放内存空间,供其他对象使用。

## 深拷贝

对于简单类型的数据来说，赋值就是深拷贝。
对于复杂类型的数据（对象）来说，才要区分浅拷贝和深拷贝。

```
var a = 1
var b = a
b = 2 //这个时候改变 b
a 完全不受 b 的影响
那么我们就说这是一个深拷贝
```
```
var a = {name: 'frank'}
var b = a
b.name = 'b'
a.name === 'b' // true
我们对 b 操作后，a 也变了
那么我们就说这是一个浅拷贝
```