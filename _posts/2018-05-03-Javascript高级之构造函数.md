---
layout:     post
title:      Javascript高级语法
subtitle:   创建对象的方式、构造函数
date:       2018-05-03
author:     BY
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Javascipt
    - 对象
    - 构造函数
    - 原型
---

 这篇博客是因为所在公司的服务器崩了，不能及时修复，所以在这段时间学习了一下Javascript的高级语法，才有此篇博客，之后也会陆续添加之后的学习笔记~

### 创建构造函数的方式

首先，如何创建一个对象？
#### 1.对象字面量---即使用键值对方式{key:value, key:value,...},如：

```
  var obj = {
    name: '张三'，
    age: 20,
    sex: '男'
  }
```
(注：此种方法只能创建一次对象，复用性较差，如果创建多个对象，代码冗余度太高)

#### 2.使用内置构造函数

```
  var obj = new Object();
  obj.name = '李四';
  obj.age = 22;
  obj.sayHello = function() {
    console.log("Hello!")
  }
```

#### 3.封装简单的工厂函数

```
  function createPerson() {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.sayHello = function() {
      console.log('Hello!')
    }
    return o;//{name:"",age:"",sayHello:function...}
  }
  
  var obj = createPerson('王五', 20);
  
  var obj = createPerson('赵六', 24);
```

#### 4.自定义构造函数

* 什么是构造函数？
构造函数其实也是函数，但是通常用来初始化对象，并且和new关键字同时出现
(注：
  *new是用来创建对象的
  *构造函数是用来初始化对象的(给对象新增成员)
  *构造函数名，首字母要大写！！！以示区分
)
如： 

```
  function Person() {
    this.name = '科比';
    this.age = 35;
    this.sayHello = function() {
      console.log('Hello!');
    }
    
    //return; // 返回新创建的对象
    //return 123; // 返回新创建的对象
    //return {}; // 返回{}, 因为{}在这里是个对象
  }
  
  var p = new Person();//new Object();
  console.log(p); //Person{age: 35, name: '科比', sayHello: f()}
  p.sayHello(); // Hello!
```

**构造函数的执行过程**

1 使用new关键字创建对象

2 调用构造函数，把新创建出来的对象赋值给构造函数内的this

3 在构造函数内使用this为新创建出来的对象新增成员

4 默认返回新创建的这个对象（普通的函数，如果不写返回语句，会返回undefined）

**构造函数的返回值**

1 若果不写返回值，默认返回的是新创建出来的对象（一般都不写这个return语句）

2 如果写return语句，return的是空值（return;）,或者是基本类型的值或者null，都会默认返回新创建出来的对象

3 如果返回的是object类型的值，将不会返回刚才新创建的对象，取而代之的是return后面的值

接着在创建一个构造函数，如下： 

```
  function Animal(name, type, barkWay) {
    this.name = name;
    this.type = type;
    this.bark = barkWay
  }
  
  var dog = new Animal('小黄', '狗子', function() {
    console.log('汪汪汪');
  });
  console.log(dog);//Animal{bark: f(), name: '小黄', type: '狗子'}
  
  var cat = new Animal('七月', '猫咪', function() {
    console.log('喵喵喵');
  })
  console.log(cat);// Animal{bark: f(), name: '七月', type: '猫咪'}
  
  //对象是无序的键值对的集合，所以打印出来的是bark,name,type,而不是name,type,bark
  //数组则是有序的，定义的是什么样打印出来的就是什么样
```

(注：如果像使用正常的函数一样使用构造函数,构造函数中this将不再指向新创建出来的对象(因为根本就没有创建对象);
构造函数中的this这个时候指向的就是window全局对象,当使用this给对象添加成员的时候,全部都添加到了window上)， 如：

```
  Animal('', '', function(){
    console.log('I am Function!');
  }); // 错误的演示
  
  window.bark(); // I am Function!表示为全局对象window添加上了一个bark方法，故需要注意上述提醒！
```

***

接着，传统构造函数存在的问题

### 构造函数

#### 1.发现问题
创建一个构造函数如下：

```
  function() {
    this.name = name;
    this.age = age;
    this.sayHi = function() {
      console.log('你好！');
    }
  }
```
调用该构造函数创建对象，并对比创建出来的对象的`sayHi`方法：

```
  var p = new Person('张三', 18);
  var p1 = new Person('李四', 19);
  console.log(p.sayHi == p1.sayHi);//输出结果为false
```

由于每个对象都是由new Person创建出来的，因此每创建一个对象，函数sayHi都会被重新创建一次，这个时候，每个对象都拥有一个独立的，但是功能完全相同的方法
,即： **功能相同的函数，完全没有必要再内存中存在这么多份。所以就造成了资源浪费。**

#### 2.解决问题
这里最好的办法就是将函数体放在构造函数之外. 在构造函数中只需要引用该函数即可。

```
  function sayHello(){
    console.log("你好");
  }

  function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayHi = sayHello;
  }

  //调用该构造函数创建对象，并对比创建出来的对象的sayHi方法
  var p = new Person("张三", 18);
  var p1 = new Person("李四", 19);
  console.log(p.sayHi == p1.sayHi); //输出结果为true
```
(注： 使用这种方式存在的问题

      1.1 全局变量增多，造成污染
      
      2.2 代码结构混乱，不易维护
)

#### 3.原型
```
  function Person(name,type) {
    this.name = name;
    this.type = type;
    this.sing = function() {
      console.log('唱歌');
    };
    this.act = function() {
      console.log('演动作戏');
    }
  }
  
  var p = new Person('张三', '歌手');
```

* 原型是什么？
在构造函数创建出来的时候，系统会默认的帮构造函数创建并关联一个神秘的对象，这个对象就是原型。
*原型默认的时候是一个空对象*

* 原型的作用？
原型中的属性和方法，可以被使用该构造函数船舰出来的对象使用

* 如何访问构造函数的原型？
```构造函数.prototype```

```
  console.log(Person.prototype);
  console.log(p.prototype);// undefined;z注意prootype是构造函数的属性，跟对象没有关系
```

* 如何给原型对象添加属性和方法？
使用对象的动态特性

```
  Person.prototype.act = function() {
    console.log('演戏剧戏');
  }
  
  p.act(); //演动作戏
```

(注： 当使用对象去访问属性和方法的时候,会首先在对象自己内部进行查找,如果找到了,就直接使用;
如果没有找到,就去原型中查找,查找到之后,使用;如果原型中还没有,如果是属性,就是undefined;如果是方法,就报错.)

```
  p.dance();//Uncaught TypeError: p.dance is not a function本身和原型中都没有 就报错
```

#### 4.使用原型解决构造函数存在的问题

```
  function Person() {
    this.name = name;
    this.age = age;
    this.sex = sex;
    // this.sayHello = function() {
    //     console.log('你好，我是'+ this.name);
    //}
  }
  
  var p = new Person('张三', 18, 'male');
  
  var p1 = new Person('李四', 19, 'male');
  
  Person.prototype.sayHello = function() {
    console.log('你好，我是' + this.name);
  }
  
  p.sayHello(); // 你好，我是张三
  p1.sayHell(); // 你好，我是李四
```
* 如何使用原型来解决构造函数存在的问题？
构造函数的原型对象中的成员，可以被该构造函数创建出来的所有对象访问，而且所有的对象共享该对象，所以
我们可以将构造函数中需要创建的函数，放到原型对象中存储，这样就解决全局变量污染的问题以及代码结构混乱的问题。

```
  Person.prototype['sing'] = function() {
    console.log('太阳当空照');
  }
  
  p.sing(); // 太阳当空照
  p1.sing(); // 太阳当空照
```
##### 常见的错误
1.将属性写在（构造函数.prototype）内

```
  function Car(name){
    this.name = name;
  }
  
  function Person() {}
  
  Person.prototype.name = '张三'; // 基本类型的属性影响不大
  
  Person.prototype.car = new Car('奔驰'); // 引用类型的属性，会被所有的对象共享
  var p = new Person();
```

2.赋值的错误

```
  function Person() {}
  
  Person.prototype.name = '张三';
  
  var p1 = new Person();
  
  var p2 = new Person();
  
  p1.name = '李四';
  
  console.log(p1.name); // 李四
  console.log(p2.name); // 张三
  
  // 如果是访问数据, 当前对象中如果没有该数据就到构造函数的原型属性中去找
  // 如果是写数据, 当对象中有该数据的时候, 就是修改值; 如果对象没有该数据, 那么就添加值
```
