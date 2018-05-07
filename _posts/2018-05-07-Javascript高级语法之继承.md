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
  
  //p对象继承了原型对象（x对象）
  //注：使用替换原型的方式实现继承的时候，原有原型中的成员就会丢失
```

* 利用混入的方式给原型对象添加成员

```
  function() Person(name, age){
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




