# JavaScript

<!-- TOC -->
- [Javascript](#JavaScript)
  - [什么是Javascript](#什么是Javascript)
  - [面向对象](#面向对象)
    - [属性类型](#属性类型)
      - [1.数据属性](#1数据属性)
      - [2.访问器属性](#2访问器属性)
  
  - [函数表单式](#函数表达式)
    - [递归](#递归)
    - [闭包](#闭包)
<!-- /TOC -->    

## 什么是Javascript

## 面向对象

### 属性类型

#### 1.数据属性

数据属性包含一个数据值的位置。在这个位置可以读取和写入值。数据属性有 4 个描述其行为的特性。

- [[Configurable]] 
表示能否通过 delete 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。

- [[Enumberable]] 
表示能否通过 for-in 循环返回属性。

- [[Writable]]
表示能否修改属性的值。

- [[Value]]
包含这个属性的值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置。这个特性的默认值为 undefined。

**数据属性可以直接定义.** 对于直接在对象上定义的属性，它们的 [[Configurable]]、[[Enumerable]] 以及 [[Writable]] 特性都被设置为 true，而 [[Value]] 特性被设置为指定的值。

例如定义如下一个对象：

```js
var person = {
  name: 'wuxiumu'
};
```

这里创建了一个名为 name 的属性，为它指定的值是 'wuxiumu'。也就是说，[[Value]] 特性将被设置为 'wuxiumu'，而对这个值的任何修改都将反映在这个位置。

**要修改默认属性的特性，必须使用 ES5 的 Object.defineProperty() 方法。** 这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符对象的属性必须是：configurable、enumerable、writable 和 value。例如：

```js
var person = {
  name: 'wuxiumu'
};

Object.defineProperty(person, 'name', {
  value: 'xiumu',
});

person.name;
// => xiumu
```

利用 Object.defineProperty 也可以创建对象的新的属性。

```js
var person = {};
Object.defineProperty(person, 'name', {
  value: 'xiumu'
});
```

如上，person 对象的 name 属性，其 [[Configurable]]、[[Enumerable]] 以及 [[Writable]] 特性默认为 false。

#### 2.访问器属性

访问器属性不包含数据值（没有 [[Value]] 特性），它们包含一对 getter 和 setter 函数（这两个函数都不是必须的）。在读取访问器属性时，会调用 getter 函数，这个函数负责返回有效的值；在写入访问器属性时，会调用 setter 并传入新值，这个函数负责决定如何处理数据。访问器属性有如下 4 个特性。

- [[Configurable]] 表示能否通过 delete 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。
- [[Enumberable]] 表示能否通过 for-in 循环返回属性。
- [[Get]] undefined 在读取属性时调用的函数。默认值为 undefined。
- [[Set]] 在写入属性时调用的函数。默认值为 undefined。

**访器属性不能直接定义** ,必须使用 Object.defineProperty() 来定义。
```js
var book = {
  _year: 2018,
  edition: 1
};

Object.defineProperty(book, 'year', {
  get: function() {
    return this._year;
  },
  set: function(newValue) {
    if (newValue > 2018) {
      this._year = newValue;
      this.edition += newValue - 2018;
    }
  }
});

book.year = 2020;
alert(book.edition);  // 3
alert(book.year); // 2018
```

以上代码创建了一个 book 对象，并给它定义了两个默认的属性：_year 和 edition。_year 前面的下划线是一种常用的记号，用于表示只能通过对象方法访问的属性（虽然理论上是可以直接访问的）。而访问器属性 year 则包含一个 getter 函数和一个 setter 函数。getter 函数返回 _year 的值，setter 函数通过计算来确定正确的版本。因此，把 year 属性修改为 2020 会导致 _year 变成 2020，而 edition 变为 3。这是使用访问器属性的常见方式，即设置一个属性的值会导致其他属性发生变化。

不一定非要同时指定 getter 和 setter。只指定 getter 意味着属性是不能写，尝试写入属性会被忽略。在严格模式下，尝试写入只指定了 getter 函数的属性会抛出错误。类似地，只指定 setter 函数的属性也不能读，否则在非严格模式下返回 undefined，严格模式下报错。

由此可以联想到数据对象与 DOM 对象的 "双向绑定"。

```js
Object.defineProperty(user, 'name', {
  get: function () {
    return document.getElementById('foo').value;
  },
  set: function (newValue) {
    document.getElementById('foo').value = newValue;
  },
  configurable: true
});
```

上面代码使用存取函数，将 DOM 对象 foo 与数据对象 user 的 name 属性，实现了绑定。两者之中只要有一个对象发生变化，就能在另一个对象上实时反映出来。

##### [[Configurable]]

把 configurable 设置为 false，表示不能从对象中删除属性，如果对这个属性调用 delete，则在非严格模式下什么都不会发生，严格模式下报错。
```js
var person = {};
Object.defineProperty(person, 'name', {
  value: 'wuxiumu',
  configurable: false // 其实默认就是 false
});

delete person.name;
alert(person.name); // wuxiumu
```

需要注意的是，当使用 var 命令声明变量时，变量的 configurable 为 false。

```js
var a1 = 1;

Object.getOwnPropertyDescriptor(this,'a1')
// Object {
//  value: 1,
//  writable: true,
//  enumerable: true,
//  configurable: false
// }
```

而不使用 var 命令声明变量时（或者使用属性赋值的方式声明变量），变量的可配置性为 true。
```js
a2 = 1;

Object.getOwnPropertyDescriptor(this,'a2')
// Object {
//  value: 1,
//  writable: true,
//  enumerable: true,
//  configurable: true
// }

// 或者写成

window.a3 = 1;

Object.getOwnPropertyDescriptor(window, 'a3')
// Object {
//  value: 1,
//  writable: true,
//  enumerable: true,
//  configurable: true
// }
```

[[Configurable]] 特性还有个作用，一旦把属性定义为不可配置的，就不能再把它变为可配置了。也就是说，当 configurable 为 false 的时候，value、writable、enumerable 和 configurable 都不能被修改了。

```js
var o = Object.defineProperty({}, 'p', {
  value: 1,
  writable: false,
  enumerable: false,
  configurable: false
});

Object.defineProperty(o,'p', {value: 2})
// TypeError: Cannot redefine property: p

Object.defineProperty(o,'p', {writable: true})
// TypeError: Cannot redefine property: p

Object.defineProperty(o,'p', {enumerable: true})
// TypeError: Cannot redefine property: p

Object.defineProperties(o,'p',{configurable: true})
// TypeError: Cannot redefine property: p
```

需要注意的是，**writable 只有在从 false 改为 true 会报错，从 true 改为 false 则是允许的。**

```js
var o = Object.defineProperty({}, 'p', {
  writable: true,
  configurable: false
});

Object.defineProperty(o,'p', {writable: false})
// 修改成功
```

至于 value，只要 writable 和 configurable 有一个为 true，就允许改动。

```js
var o1 = Object.defineProperty({}, 'p', {
  value: 1,
  writable: true,
  configurable: false
});

Object.defineProperty(o1,'p', {value: 2})
// 修改成功

var o2 = Object.defineProperty({}, 'p', {
  value: 1,
  writable: false,
  configurable: true
});

Object.defineProperty(o2,'p', {value: 2})
// 修改成功
```
##### [[Enumerable]]

enumerable 存放一个布尔值，表示该属性是否可枚举，如果设为 false，会使得某些操作（比如 for-in 循环、Object.keys() 以及 JSON.stringify 会跳过该属性）

```js
var obj = {
  a: 10,
  b: 20
};

Object.defineProperty(obj, 'c', {
  value: 30,
  enumerable: false
});

for (var key in obj)
  console.log(key, obj[key]);

// a 10
// b 20
```

##### [[Writable]]

[[Writable]] 特性只存在于数据属性中，一旦被设置为 false，那么该属性值就不能被修改（只读）。如果尝试为它指定新值，则在非严格模式下，会被忽略，严格模式下报错。

```js
var person = {};
Object.defineProperty(person, 'name', {
  value: 'wuxiumu',
  writable: false // 其实默认就是 false
});

person.name = 'xiumu';
alert(person.name); // xiumu
```

不过如果是用 Object.defineProperty 对属性重新赋值，就会直接报错。

```js
var person = {};
Object.defineProperty(person, 'name', {
  value: 'wuxiumu',
  writable: false // 其实默认就是 false
});

Object.defineProperty(person, 'name', {
  value: 'xiumu',
});

// Uncaught TypeError: Cannot redefine property: name
```

当然前面也提到了，如果 writable 为 false，但是 configurable 为 true，还是可以对属性重新赋值的。

##### 其他


我们可以用 Object.defineProperties() 方法同时定义多个属性。

```js
var person = {};
Object.defineProperties(person, {
  name: {
    value: 'wuxiumu',
    writable: true
  },
  age: {
    value: 30,
    enumerable: true
  }
});
```

我们可以用 Object.getOwnPropertyDescriptor() 方法取得给定属性的描述符。返回是一个对象，如果是数据属性，这个返回对象的属性有 configurable、enumerable、writable 以及 value；如果是访问器属性，则这个对象的属性有 configurable、enumerable、get 以及 set。

```js
var person = {};
Object.defineProperty(person, 'name', {
  value: 'wuxiumu',
  configurable: false // 其实默认就是 false
});

var descriptor = Object.getOwnPropertyDescriptor(person, 'name');
console.log(descriptor);
// Object {value: "xiumu", writable: false, enumerable: false, configurable: false}


var book = {
  _year: 2018,
  edition: 1
};

Object.defineProperty(book, 'year', {
  get: function() {
    return this._year;
  },
  set: function(newValue) {
    if (newValue > 2018) {
      this._year = newValue;
      this.edition += newValue - 2018;
    }
  }
});

Object.getOwnPropertyDescriptor(book, 'year');
// 可以自行打印看看，4 个 key
```

一个属性只能是数据属性或者访问器属性，所以均拥有 [[Configurable]] 和 [[Enumerable]] 两个特性，但是对于其他两个属性，则分别拥有，所以不可能同时有 [[Value]] 和 [[Set]]，[[Writable]] 和 [[Set]] 等（如果同时定义则会报错）


## 函数表达式

定义函数有两种

```js

//函数声明
function functionName1(arg){}
console.log(functionName1.name) //functionName1

//函数表达式
var functionName2 = function (arg){}
console.log(functionName2.name) //functionName2
```

### 递归

```js
// # 递归
function factorial(num){
    if(num <= 1){
        return 1
    }
    return num + factorial(num-1)
}

function factorial_better(num){
    if(num <= 1){
        return 1
    }
    return num + arguments.callee(num-1)
}

var factorial_best = (function f(num){
    if(num <= 1){
        return 1
    }
    return num + f(num-1)
})

console.log(factorial(100)) //5050
console.log(factorial_better(100)) //5050
console.log(factorial_best(100)) //5050
```

### 闭包

#### 1.什么是闭包性

闭包，官方对闭包的解释是：一个拥有许多变量和绑定了这些变量的环境的表达式（通常是一个函数），因而这些变量也是该表达式的一部分。闭包的特点：
- 1. 作为一个函数变量的一个引用，当函数返回时，其处于激活状态。
- 2. 一个闭包就是当一个函数返回时，一个没有释放资源的栈区。
　
　　简单的说，Javascript允许使用内部函数---即函数定义和函数表达式位于另一个函数的函数体内。而且，这些内部函数可以访问它们所在的外部函数中声明的所有局部变量、参数和声明的其他内部函数。当其中一个这样的内部函数在包含它们的外部函数之外被调用时，就会形成闭包。
  
#### 2.闭包的几种写法和用法

首先要明白，在JS中一切都是对象，函数是对象的一种。下面先来看一下闭包的5种写法，简单理解一下什么是闭包。

```js
//第1种写法  
function Circle(r) {  
      this.r = r;  
}  
Circle.PI = 3.14159;  
Circle.prototype.area = function() {  
  return Circle.PI * this.r * this.r;  
}  
  
var c = new Circle(1.0);     
alert(c.area());
```

这种写法没什么特别的，只是给函数添加一些属性。

```js
//第2种写法  
var Circle = function() {  
   var obj = new Object();  
   obj.PI = 3.14159;  
     
   obj.area = function( r ) {  
       return this.PI * r * r;  
   }  
   return obj;  
}  
  
var c = new Circle();  
alert( c.area( 1.0 ) );
```

这种写法是声明一个变量，将一个函数当作值赋给变量。

```js
//第3种写法  
var Circle = new Object();  
Circle.PI = 3.14159;  
Circle.Area = function( r ) {  
       return this.PI * r * r;  
}  
  
alert( Circle.Area( 1.0 ) );
```

这种方法最好理解，就是new 一个对象，然后给对象添加属性和方法。
```js
//第4种写法  
var Circle={  
   "PI":3.14159,  
 "area":function(r){  
          return this.PI * r * r;  
        }  
};  
alert( Circle.area(1.0) );
```

这种方法使用较多，也最为方便。var obj = {}就是声明一个空的对象。

```js
//第5种写法  
var Circle = new Function("this.PI = 3.14159;this.area = function( r ) {return r*r*this.PI;}");  
  
alert( (new Circle()).area(1.0) ); 
```

总的来说，上面几种方法，第2中和第4中较为常见;
上面代码中出现了JS中常用的Prototype，那么Prototype有什么用呢？下面我们来看一下：
```js
var dom = function(){
        
    };

    dom.Show = function(){
        alert("Show Message");
    };
    
    dom.prototype.Display = function(){
        alert("Property Message");
    };

    dom.Display(); //error
    dom.Show();  
    var d = new dom();
    d.Display();
    d.Show(); //error
```

我们首先声明一个变量，将一个函数赋给他，因为在Javascript中每个函数都有一个Portotype属性，而对象没有。添加两个方法，分别直接添加和添加打破Prototype上面，来看下调用情况。分析结果如下：

- 1、不使用prototype属性定义的对象方法，是静态方法，只能直接用类名进行调用！另外，此静态方法中无法使用this变量来调用对象其他的属性！
- 2、使用prototype属性定义的对象方法，是非静态方法，只有在实例化后才能使用！其方法内部可以this来引用对象自身中的其他属性！

下面我们再来看一段代码：

```js
var dom = function(){
        var Name = "Default";
        this.Sex = "Boy";
        this.success = function(){
            alert("Success");
        };
    };

    alert(dom.Name);
    alert(dom.Sex);
```

再来看看这种写法，其实这是Javascript的一个"语法糖"，这种写法相当于：

```js
var html = new Object();
    html.Name = 'Object';
    html.Success = function(){
            this.Say = function(){
                    alert("Hello,world");
            };
            alert("Obj Success");
```

变量html是一个对象，不是函数，所以没有Prototype属性，其方法也都是公有方法，html不能被实例化

但是他可以作为值赋给其它变量，如var o = html; 我们可以这样使用它：

```js
alert(html.Name);
    html.Success();
```

说到这里，完了吗？细心的人会问，怎么访问Success方法中的Say方法呢？是html.Success.Say()吗？

当然不是，上面刚说过由于作用域的限制，是访问不到的。所以要用下面的方法访问：

```js
var s = new html.Success();
s.Say();

//还可以写到外面
html.Success.prototype.Show = function(){
    alert("HaHa");
};
var s = new html.Success();
s.Show();
```

关于Javascript作用域的问题，不是一两句能说清楚的，有兴趣的大家可以网上找些资料看看。

#### 2.Javascript闭包的用途

事实上，通过使用闭包，我们可以做很多事情。比如模拟面向对象的代码风格；更优雅，更简洁的表达出代码；在某些方面提升代码的执行效率。

- 1、匿名自执行函数

　　我们知道所有的变量，如果不加上var关键字，则默认的会添加到全局对象的属性上去，这样的临时变量加入全局对象有很多坏处，
比如：别的函数可能误用这些变量；造成全局对象过于庞大，影响访问速度(因为变量的取值是需要从原型链上遍历的)。
除了每次使用变量都是用var关键字外，我们在实际情况下经常遇到这样一种情况，即有的函数只需要执行一次，其内部变量无需维护，
比如UI的初始化，那么我们可以使用闭包：

```js
var data= {    
    table : [],    
    tree : {}    
};    
     
(function(dm){    
    for(var i = 0; i < dm.table.rows; i++){    
       var row = dm.table.rows[i];    
       for(var j = 0; j < row.cells; i++){    
           drawCell(i, j);    
       }    
    }    
       
})(data);
```

我们创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在函数执行完后会立刻释放资源，关键是不污染全局对象。

- 2、结果缓存

我们开发中会碰到很多情况，设想我们有一个处理过程很耗时的函数对象，每次调用都会花费很长时间，

那么我们就需要将计算出来的值存储起来，当调用这个函数的时候，首先在缓存中查找，如果找不到，则进行计算，然后更新缓存并返回值，如果找到了，直接返回查找到的值即可。闭包正是可以做到这一点，因为它不会释放外部的引用，从而函数内部的值可以得以保留。

```js
var CachedSearchBox = (function(){    
    var cache = {},    
       count = [];    
    return {    
       attachSearchBox : function(dsid){    
           if(dsid in cache){//如果结果在缓存中    
              return cache[dsid];//直接返回缓存中的对象    
           }    
           var fsb = new uikit.webctrl.SearchBox(dsid);//新建    
           cache[dsid] = fsb;//更新缓存    
           if(count.length > 100){//保正缓存的大小<=100    
              delete cache[count.shift()];    
           }    
           return fsb;          
       },    
     
       clearSearchBox : function(dsid){    
           if(dsid in cache){    
              cache[dsid].clearSelection();      
           }    
       }    
    };    
})();    
     
CachedSearchBox.attachSearchBox("input");
```

这样我们在第二次调用的时候，就会从缓存中读取到该对象。

- 3、封装

```js
var person = function(){    
    //变量作用域为函数内部，外部无法访问    
    var name = "default";       
       
    return {    
       getName : function(){    
           return name;    
       },    
       setName : function(newName){    
           name = newName;    
       }    
    }    
}();    
     
print(person.name);//直接访问，结果为undefined    
print(person.getName());    
person.setName("abruzzi");    
print(person.getName());    
   
得到结果如下：  
   
undefined  
default  
abruzzi
```

- 4、实现类和继承

```js
function Person(){    
    var name = "default";       
       
    return {    
       getName : function(){    
           return name;    
       },    
       setName : function(newName){    
           name = newName;    
       }    
    }    
    };   

    var p = new Person();
    p.setName("Tom");
    alert(p.getName());

    var Jack = function(){};
    //继承自Person
    Jack.prototype = new Person();
    //添加私有方法
    Jack.prototype.Say = function(){
        alert("Hello,my name is Jack");
    };
    var j = new Jack();
    j.setName("Jack");
    j.Say();
    alert(j.getName());
```

我们定义了Person，它就像一个类，我们new一个Person对象，访问它的方法。

下面我们定义了Jack，继承Person，并添加自己的方法。

##个人总结：

Javascript比我们想象的要更加强大和灵活，要学好需要付出很大的努力~~
