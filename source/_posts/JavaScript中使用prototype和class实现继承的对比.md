---
title: JavaScript中使用prototype和class实现继承的对比
date: 2019-04-08 14:09:34
tags:
---

## 继承的两种写法

ES5写法

```
 function Human(name){
     this.name = name
 }
 Human.prototype.run = function(){
     console.log("我叫"+this.name+"，我在跑")
     return undefined
 }
 function Man(name){
     Human.call(this, name)
     this.gender = '男'
 }

 var f = function(){}
 f.prototype = Human.prototype
 Man.prototype = new f()
 //上面三行等价于Man.prototype.__proto__ = Human.prototype
 //但直接修改__proto__开销很高

 Man.prototype.fight = function(){
     console.log('糊你熊脸')
 }

```

ES6写法

```
 class Human{
     constructor(name){
         this.name = name
     }
     run(){
         console.log("我叫"+this.name+"，我在跑")
         return undefined
     }
 }
 class Man extends Human{
     constructor(name){
         super(name)
         this.gender = '男'
     }
     fight(){
         console.log('糊你熊脸')
     }
 }
```

## 两种方法的对比

ES5更为直观，便于理解。
ES6语法糖写起来容易，但继承属性不方便，需要写成函数然后return属性值。