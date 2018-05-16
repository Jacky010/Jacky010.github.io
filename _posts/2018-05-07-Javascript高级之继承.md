---
layout:     post
title:      Javascript高级语法
subtitle:   继承
date:       2018-05-07
author:     BY Jacky
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Javascipt
    - 继承 
    - 原型链
---

### 继承
通俗的讲就是当前没有的属性和方法，别人有拿过来用，就是继承。

### 继承的实现方式
1 最简单的继承

```
  var obj = {
    name: '张三',
    age: 18,
    sayHello: function(){
        console.log('Hello!');
    }
  }
  
  var o = {}
  
  // 混入式继承
  for(var k in obj){
    o[k] = obj[k]
  }
  console.log(o); // o{name: '张三', age: 18, sayHello: f(){}}
```

2 原型继承
利用原型中的成员可以被其他相关的对象共享这一特性，可以实现继承，这种实现继承的方式就叫原型继承。

* 给原型对象中添加成员，通过对象的动态特性（不是严格意义上的继承）

```
  function Person(name, age){
    this.name = name;
    this.age = age;
  }
  Person.prototype.sayHello = function() {
    console.log('Hello!');
  }
  
  var p = new Person('张三', 18);
  p.sayHello();
  
  //p对象就继承原型
```

* 直接替换原型对象

```
  function Person(name, age){
    this.name = name;
    this.age = age;
  }
  Person.prototype.sayHi = function(){
    console.log('Hi!');
  }
  var x = { 
    sayHello: function(){
      console.log('Hello!');
    }
  }
  
  person.Prototype = x;
  var p = new Person('张三', 18);
  p.sayHello();
  //p.sayHi(); // 替换之后，sayHi方法不复存在
  
  // p对象继承了原型对象（x对象）
  // 注：使用替换原型的方式实现继承的时候，原有原型中的成员就会丢失
```

* 利用混入的方式给原型对象添加成员

```
  function Person(name, age){
    this.name = name;
    this.age = age;
  }
  
  Person.prototype.sayHi = function(){
    console.log('Hi');
  }
  
  var x = {
    sayHello: function(){
      console.log('Hello!');
    }
  }
  for(var k in x){
    Person.prototype[k] = x[k];
  }
  
  var p = newPerson('张三', 18);
  p.sayHello();
  
  // p继承自原型对象
```

*  经典继承的语法

```
  var obj = {
    name: '张三'
  }；
  
  var o = Object.create(obj);
  
  console.log(o.name); // 张三
  
  // Object.create(obj)返回值为一个对象，继承自参数中的obj
```

上述这个方法是ES5中提出的，故存在兼容问题：

检验浏览器的能力，如果没有Object.create方法就给其添加一个，如：

```
  if(Object.create){
    var o = Object.create(obj);
  }else{
    Object.create = function(){
        function F(){};
        F.prototype = obj;
        var o = new F();
    }
    var o = Object.create(obj);
  }
  // 不推荐使用，每次都要去判断不方便，造成代码冗余
```

自己封装函数用于检测浏览器：

```
  function isCreate(obj){
    if(Object.create){
        return Object.create(obj);
    }else{
        function F(){};
        F.prototype = obj;
        return new F();
    }
  }
```

### 原型链

1 什么是原型链？

* 每个构造函数都有原型对象
* 每个对象都会有构造函数
* 每个构造函数的原型都是一个对象
* 那么这个原型对象也会有构造函数
* 那么这个原型对象的构造函数也会有原型对象
* 这样就会形成一个链式的结构，称为原型链

2 原型链结构的基本形式？

```
  function Person(name){
    this.name = name;
  }
  
  var p = new Person();
  
  //p ---> Person.prototype ---> Object.prototype ---> null
```

如图：

![pic4 icon](http://f.cl.ly/items/3A1U2G0B460I1T0T192g/yxl.png)

3 属性搜索原则：

* 当访问一个对象的成员的时候，会先在自身找有没有，如果找到直接使用；
* 若没有找到，则去当前对象的原型对象中去查找，若找到直接使用；
* 若没有找到，继续去找原型对象的原型对象，若找到直接使用；
* 若没有找到，则继续想上查找，知道Object.prototype, 若还是没有，就报错。

4 原型继承概念：

通过修改原型链结构实现的继承，就称为原型继承。

5 复杂的原型链:

光说不练假把式，那现在来写一个复杂一点的原型链.

```
  // 小学 ---> 初中 ---> 高中 ---> 大学
  function PrimarySchool(){
    this.stage1 = '小学';
  }
  
  MiddleSchool.prototype = new PrimarySchool()
  MiddleSchool.prototype.constructor = PrimarySchool;
  
  function MiddleSchool(){
    this.stage2 = '初中';
  }
  
  HighSchool.prototype = new MiddleSchool()
  HighSchool.prototype.constructor = MiddleSchool;
  
  function HighSchool(){
    this.stage3 = '高中';
  }
  
  College.prototype = new HighSchool()
  College.prototype.constructor = HighSchool;
  
  function College(){
    this.stage4 = '大学';
  }
  
  var Human = new College();
  console.log(Human);
```

### Object.prototype的成员

1 constructor

原型对象内的一个属性，指向该原型对象相关联的构造函数

2 hasOwnProperty

判断对象本身（不包含原型）是否拥有某个属性

```
  function Person(){
    this.name = '张三';
  }
  
  Person.prototype.name = '李四';
  
  var p = new Person();
  console.log(p.name); // 李四
  console.log(p.hasOwnProperty('__proto__'); // false
```

3 toString  和 toLocaleString

```
  var o = {};
  console.log(o.toString()); // [object Object]
  console.log(o.toLocaleString()); // [object Object]
  
  var now = new Date();
  console.log(now.toString()); // Mon May 07 2018 14:12:35 GMT+0800 (中国标准时间)
  console.log(now.toLocaleString()); // 2018/5/7 下午2:12:35
```
4 valueOf

获取当前对象的值

```
  function Person(){
    this.valueOf = function(){
        return 123;
    }
  }
  
  var p = new Person();
  
  console.log( 1 + p); // 124
  // 在对象参与运算的时候，默认的会先去调用对象的valueOf方法；若valueOf获取到的值，无法进行运算，就去调用p的toString方法
  // 最终做的就是字符串拼接的工作
```

5 __proto__

原型对象对象中属性，可以使用`对象.__proto__`去访问原型对象


### 其他

#### arguments对象
函数内部的一个对象，当函数调用时，系统会将所有的实参依次存入这个数组对象。

栗子1： 传入任意个参数，返回最大数字

```
  function max(){
    var maxNum = arguments[0];
    for(var i = 1;i < arguments.length; i++){
        maxNum = maxNum > arguments[i] ? maxNum : arguments[i];
    }
    return maxNum;
  }
  console.log(max(1, 2, 3, 0, 6)); // 6 
```

栗子2： 数组去重

```
  function distinct(){
    var arr = [];
    for(var i = 0; i< arguments.length; i++){
        if(arr.indexOf(arguments[i]) == -1){
            arr.push(arguments[i]);
        }
    }
    return arr;
  }
  console.log(distinct(1, 2, 2, 1, 3, 4)); // [1, 2, 3, 4]
```

注意： 

* 一个函数有形参的时候，调用的时候，可以不传参数
* 一个函数没有形参的时候，调用的时候，可以传参（arguments对象）
* 一个函数不管有没有形参，调用的时候都会把实参的值存入arguments对象


### 结束

在此，Javascript高级语法之面向对象这一块就弄得很清楚了，涨了不少姿势啊，共勉。。。

