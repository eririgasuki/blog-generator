---
title: JavaScript的数据类型
date: 2018-12-31 11:23:04
tags:
---

# 七种数据类型

number, string, boolean, symbol, null, undefined, object。

## number

### 整数和浮点数
JavaScript 内部，所有数字都是以64位浮点数形式储存，即使整数也是如此。
这就是说，JavaScript 语言的底层根本没有整数，所有数字都是小数（64位浮点数）。
```
1 === 1.0 // true

0.1 + 0.2 === 0.3 // false

0.3 / 0.1 // 2.9999999999999996
```

### 数值精度
JavaScript 对15位的十进制数都可以精确处理。

### 数值范围
JavaScript 能够表示的数值范围为21024到2-1023（开区间）。
```
Math.pow(2, 1024) // Infinity
Math.pow(2, -1075) // 0

var x = 0.5;
for(var i = 0; i < 25; i++) {
  x = x * x;
}
x // 0

Number.MAX_VALUE // 1.7976931348623157e+308
Number.MIN_VALUE // 5e-324
```

### 数值的表示法

0开头八进制需特别注意，容易出bug。

* 整数和小数：`1 1.1 .1`
* 科学记数法：`1.23e2`
* 二进制：`0b11`
* 八进制：`011（后来 ES5 添加了 0o11 语法）`
* 十六进制：`0x11`
* NaN： `5 - 'x' // NaN`

## string

字符串就是零个或多个排在一起的字符，放在单引号或双引号之中。

### 转义
反斜杠（\）在字符串内有特殊含义，用来表示一些特殊字符，所以又称为转义符。
需要用反斜杠转义的特殊字符，主要有下面这些。
* `\0` ：null（\u0000）
* `\b` ：后退键（\u0008）
* `\f` ：换页符（\u000C）
* `\n` ：换行符（\u000A）
* `\r` ：回车键（\u000D）
* `\t` ：制表符（\u0009）
* `\v` ：垂直制表符（\u000B）
* `\'` ：单引号（\u0027）
* `\"` ：双引号（\u0022）
* `\\` ：反斜杠（\u005C）

### 字符集与base64转码
JavaScript 使用 Unicode 字符集。
Base64 就是一种编码方法，可以将任意值转成 0～9、A～Z、a-z、+和/这64个字符组成的可打印字符。使用它的主要目的，不是为了加密，而是为了不出现特殊字符，简化程序的处理。
要将非 ASCII 码字符转为 Base64 编码，必须中间插入一个转码环节，再使用这两个方法。
```
function b64Encode(str) {
  return btoa(encodeURIComponent(str));
}

function b64Decode(str) {
  return decodeURIComponent(atob(str));
}

b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```

### 多行字符串

换行时用`''+''`可读性更好更不易出错。
```
var s = '12345' +
            '67890' // 无回车符号
 
var s = `12345
67890` // 含回车符号
```

## boolean
布尔值代表“真”和“假”两个状态。“真”用关键字true表示，“假”用关键字false表示。
除了下面六个值被转为false，其他值都视为true。
* undefined
* null
* false
* 0
* NaN
* ""或''（空字符串）

## symbol
Symbol 可以创建一个独一无二的值（但并不是字符串）。
Symbol 生成一个全局唯一的值。
```
var a1 = Symbol('a')
var a2 = Symbol('a')
a1 !== a2 // true
```

## null和undefined
都表示没有值。

1. （规范）如果一个变量没有被赋值，那么这个变量的值就是 `undefiend`
2. （习俗）如果你想表示一个还没赋值的对象，就用 `null`。如果你想表示一个还没赋值的字符串/数字/布尔/symbol，就用 `undefined`

大多数情况下，我们都应该用null。undefined仅仅在判断函数参数是否传递的情况下有用。

## object
对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。

* object 就是上面几种基本类型（无序地）组合在一起
* object 里面可以有 object
* object 的 key 一律是字符串，不存在其他类型的 key
* `object['']` 是合法的
* `object['key']` 可以写作 `object.key`
* `object.key` 与 `object[key]` 不同
* `delete object['key']`
* `'key' in object`

xxx 的类型|string|number|boolean|symbol|undefined|null|object|function
:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:
typeof xxx|'string'|'number'|'boolean'|'symbol'|'undefined'|'object'|'object'|'function'

注意 function 并不是一个类型