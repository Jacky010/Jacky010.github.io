---
layout:     post
title:      Javascript高级语法
subtitle:   原型
date:       2018-05-07
author:     BY Jacky 
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Javascipt
    - 原型
---


继续javacript高级语法之旅。。。

## 原型
### 原型的相关概念
#### 神秘对象与构造函数

* 神秘对象就是构造函数的"原型属性"
* 简称原型（构造函数的原型）
#### 神秘对象与构造函数所创建出来的对象

* 神秘对象针对构造函数创建出来的对象称为"原型对象"
* 简称原型（对象的原型）

```
  function Person(){
    this.name = name;
    this.sayHello = function(){
      console.log('Hello!');
    }
  }
  
  var p = new Person('张三');
  p.sayHello();
```

![pic1 icon](http://f.cl.ly/items/1l0a290P3f0C3s111f0z/03-4.png)

原型的三角关系(初级)：

![pic2 icon](http://f.cl.ly/items/151s2V3c3l300i1z1A1y/001.png)

### 原型的使用

* 使用对象的动态特性给原型对象添加成员

```
  function Person(){}
  Person.prototype.sayHello = function(){
    console.log('Hello!')
  }
  
  var p = new Person();
  p.sayHello();
```

* 直接替换原型对象

```
  function Person(){}
  Person.prototype = {
    sayHello: function(){
      console.log('Hello!');
    }
  };
  
  var p = Person();
  p.sayHello();
  // 如果使用第二种方式使用原型，那么会出现如下问题：
  // 载体还原之前创建的对象的原型和在替换原型对象之后创建的对象的原型不是同一个！！！
```

### 使用原型的注意事项

* 使用对象访问属性的时候，如果本身内找不到就会去原型中查找，但是使用点语法进行属性赋值的时候，并不会去原型中查找；
使用点语法进行赋值的时候，如果对象不存在该属性，就会给该对象新增该原型，而不会去修改原型中的属性

```
  function Person(){}
  Person.prototype.name = '张三';
  Person.prototype.age = 18;
  
  var p =new Person();
  // 在Person中没找到在prototype中找到name
  console.log(p.name); // 张三
  
  // 这里Person没有name属性,所以新增原型
  p.name = '李四'; 
  console.log(p.name); // 李四
```

* 如果在原型中的属性是引用类型的属性，那么所有的对象共享该属性，并且一个对象修改了该引用类型属性中的成员，其他对象也都会受影响

```
  function Person(){}
  
  var car = {
    brand: 'jeep';
  }
  
  // car为引用类型
  Person.prototype.car = car;
  
  var p = new Person();
  console.log(p.car.brand); // jeep
  
  // 修改属性
  Person.prototype.car = {
    brand: 'benz';
  }
  
  var p1 = new Person();
  console.log(p1.car.brand); // benz
```

* 一般情况下不会将属性放到原型对象中，一般情况下原型中只会方需要共享的方法

### __proto__属性

* 如何访问原型？

1 通过构造函数访问原型

`构造函数.prototype`

```
  function Person(){}
  
  Person.prototype.msg = 'Hello!';
  var p = new Person();
```

2 通过对象问原型

即使用__proto__属性：是一个非标准的属性（为了保证通用性，这个属性不推荐使用），主要用来做调试。

```
  function Person(){}
  
  var p = new Person();
  
  p.__proto__.sayHello = function(){
    console.log('Hello!');
  }
  p.sayHello(); // Hello!
```
原型三角关系：

![pic3 icon](http://f.cl.ly/items/2d0n0O1w1y3e1r0P033e/002.png)

### 构造函数属性相关的问题 

```
  function Person(){}
  console.log(Person.prototype.constructor); // function Person(){}
 
  Person.prototype = {
    //constructor: Person;
  }
  
  // 若不加constructor: Person;输出为：function Object(){}
  // 加constructor: Person;输出为： function Person(){}
  console.log(Person.prototype.constructor);
  
  // 在使用新的对象替换默认的原型对象之后，原型对象中的constructor属性会变成Object
  // 为了保证整个 构造函数---原型---对象之间关系的合理性
  // 应做如下操作：
  // 在替换原型对象的时候，在新的原型对象中手动添加constructor属性
```
