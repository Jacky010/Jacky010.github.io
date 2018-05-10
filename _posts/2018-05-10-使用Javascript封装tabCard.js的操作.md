---
layout:     post
title:      使用Javascript封装tabCard.js的操作
subtitle:   封装插件
date:       2018-05-10
author:     BY Jacky
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Javascipt
    - 原型 
    - 闭包
    - 上下文
---


这篇博客相当于是把之前几篇博客的知识加上其他知识点结合起来封装了一个用于选项卡的点击切换、自动切换功能，功能是针对选项卡这一组件来说够用了，
在开发项目使用选项卡的点击切换功能较多，至于自动切换功能更多的是使用在商城网站上展示商品。话不多说开始封装之路吧~~~

### 封装
1）定义一个对象TabCard

2）使用原型替换TabCard.prototype={}

3）进行初始化操作，主要初始化获取元素、注册事件和自动切换操作

4）编写获取元素，注册事件和自动切换操作代码

5）完成之后将TabCard暴露出去，对外开放

那开始用代码完成上述过程吧~~~

```
  (function(window){
    var TabCard = function(config){
      // 初始化
      this.init(config);
    };

    TabCard.prototype = {
      init: function(config){
        // 初始化获取元素
        initEles.call(this, config);
        // 初始化事件注册
        initEvents.call(this);
        // 初始化自动切换
        if(this.auto === true) {
          this.autoToggle();
        }
      },
      
      // 改变样式
      changeStyle: function(obj){
        var tabMains = this.tabMains;
        var tabMenus = this.tabMenus;
        for(var i = 0; i < tabMenus.length; i++){
          // 清除选项卡按钮样式
          tabMenus[i].className = 'tab-item';
          // 隐藏全部div
          tabMains[i].style.display = 'none';
        }
        // 激活状态样式
        obj.className += ' active';
        // 显示div
        tabMains[obj.index].style.display = "block";
      },
      
      // 自动切换
      autoToggle: function(){
        var index = -1;
        var that = this;
        var toggle = function(){
          index++;
          if(index > 3){
            index = 0;
          }
          that.changeStyle(that.tabMenus[index]);
        }
        toggle();
        setInterval(toggle, 1500); // 1.5s自动切换
      }
    };
    
    // 获取元素id
    var id = function(id){
      return document.getElementById(id);
    };
    
    // 根据config里的id赋值
    var initEles = function(config){
      this.tabMenus = id(config.tabMenu).children;
      this.tabMains = id(config.tabMain).children;
      this.auto = config.auto;
    };
  
    // 注册事件
    var initEvents = function(){
      var that = this; // that存储当前对象也就TabCard创建出来的对象
      for(var i = 0; i < this.tabMenus.length; i++){
        this.tabMenus[i].index = i; // 初始化索引
        this.tabMenus[i].onclick = function(){
          // that还是只想TabCard创建出来的对象
          // this指的就是当前点击事件触发的tabMenu的子元素
          that.change(this);
        }
      }
    };
    
    // 暴露出去
    window.TabCard = TabCard;
  })(window);
  
```
### 使用

html:

```
  <div class="container">
    <ul class="tab-list" id="tab-menu"> <!--一定要有id!!!-->
        <li class="tab-item active">选项卡1</li>
        <li class="tab-item">选项卡2</li>
        <li class="tab-item">选项卡3</li>
        <li class="tab-item">选项卡4</li>
    </ul>
    <div class="tab-wrap" id="tab-main"><!--一定要有id!!!-->
        <div class="main selected">
            <a href="#"><img src="img/pic1.jpg" alt=""/></a>
        </div>
        <div class="main">
            <a href="#"><img src="img/pic2.jpg" alt=""/></a>
        </div>
        <div class="main">
            <a href="#"><img src="img/pic3.jpg" alt=""/></a>
        </div>
        <div class="main">
            <a href="#"><img src="img/pic4.jpg" alt=""/></a>
        </div>
    </div>
  </div>
```

js:

```
  <script type="text/javascript" src="js/tabCard.js"></script>
  <script type="text/javascript">
    var tab = new TabCard({
        tabMenu: "tab-menu",    // 指定tabCard栏菜单id
        tabMain: "tab-main",    // 指定tabCard栏内容id
        auto: true              // 是否自动切换
    });
</script>
```

css:

```
  * {
    margin: 0;
    padding: 0;
}
ul {
    list-style: none;
}
.container {
    width: 1000px;
    height: 475px;
    margin: 0 auto;
    margin-top: 100px;
}

.tab-list {
    border: 1px solid #ddd;
    border-bottom: 0;
    height: 36px;
    width: 320px;
}

.tab-list li {
    position: relative;
    float: left;
    width: 80px;
    height: 34px;
    line-height: 34px;
    text-align: center;
    cursor: pointer;
    border-top: 4px solid #fff;
}

.tab-wrap {
    width: 1002px;
    border: 1px solid #ddd;
    height: 476px;
}

.tab-wrap .main {
    float: left;
    display: none;
}

.tab-wrap .main.selected {
    display: block;
}

.tab-list li.active {
    border-color: red;
    border-bottom: 0;
}
```
### 其他

* 沙箱

与外界个觉得一个环境，外界无法修改该环境内的任何信息，沙箱内的东西单独属于一个世界。


```
  (function(){
    var a = 1;
  })();
  // 实现原理
  // 函数可以构建作用域，上级作用域不能直接访问下级作用域中的数据。
```

* 函数的调用模式

1） 函数模式

```
  function foo(){
    console.log(this); //this指向window全局对象
  }
  foo();
```

2） 方法模式

```
  var obj = {
    foo: function(){
      console.log(this); // this指向调用这个方法的对象
    }
    obj.foo();
  }
```

3） 构造函数模式

```
  function Person(){
    console.log(this); // this使用new创建出来的对象
  }
  var o = new Person();
```

4） 上下文模式

在上下文调用模式中，可以修改this的值，即可以修改函数的调用方式。

使用如下方法，可以修改函数调用上下文：

apply: `函数.apply(对象, [函数需要参数，数组类型]);  //可传可不传` 
 
```
  function foo(a,b){
    console.log(this.name + (a*b) + '岁' );
  }
  var obj = {
    name: '张三'
  }
  foo.apply(obj, [2, 10]); //张三20岁
```

call:  `函数.call(对象, 参数1, 参数2, ..., 参数n);  //参数1...n 可传可不传`

```
  function foo(a,b){
    console.log(this.name + (a*b) + '岁' );
  }
  var obj = {
    name: '张三'
  }
  foo.call(obj, 3, 20); //张三60岁
```

两者的区别：

1）第一个参数都是把this修改成对象

2）当函数需要参数时，那么apply是用数组进行参数的传递；而call是使用单个单个的参数进行传递；call用于确定了函数的形参有多少个的时候使用，
apply用于函数的形参个数不确定的情况。

注：apply方法和call方法第一个参数传递null或undefined时，表示为函数调用模式，即this的指向为window对象。

```
  // 求数组中的最大值
  var arr = [5, 1, 9, 0, 24];
  var max = Math.max.apply(null, arr);
  console.log(max); // 24
  
  //将传入的参数用-相互连接
  function foo(){
    var str = [].join.apply(arguments, ["-"]);
    return str;
  }
  var str = foo(2018, 05, 10, '下午' );
  console.log(str); // 2018-5-10-下午
```

当使用call和apply传入的第一个参数为值类型的时候，会将值类型转换成对应的对象(引用类型),然后赋值给this。

* 补充

借用构造函数实现继承

```
  function Person(){
    this.name = '张三';
    this.age = 18;
  }
  
  function Student(){
    var stu = this;
    Person.apply(stu);
  }
  
  var p = new Student();
  console.log(p); // Student {name: "张三", age: 18}
```
