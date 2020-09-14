---
layout:     post
title:      "【读书笔记】设计模式与继承"
subtitle:   ""
author:     "wml"
header-img: "img/bg-1.png"
header-mask:  0.5
catalog: true
tags:
    - js
---

### 对象属性

属性类型： 数据属性和访问器属性；

#### 对象的数据属性有四种特性：

configurable: 表示能否通过delete删除属性从而重新定义属性（不可删），默认true（设置为false后不能设置回来）

enumberable: 表示能否通过for-in循环返回属性， 是否可枚举（不可读），默认true

writable: 表示能否修改属性的值（不可写），默认true

value: 表示包含属性的数据值，默认undefined；

修改属性特性，必须使用object.defineProperty()方法

```js

// configurable
var person = {
  name: 'wml',
  age: 18
};
console.log(person.name); // wml
object.defineProperty(person, 'name', {
  configurable: false;
});
delete person.name;
console.log(person.name); // wml

// enumberable(defienProperty为对象添加属性，默认对象的数据属性都为false)
Object.defineProperty(person, 'sex', {
enumberable: false,
value: 'female'
});

for(let i in person) {
    console.log(i, person[i]) // name, age
}

Object.getOwnPropertyDescriptor(person, 'sex'); //{value: 'female', writable: false, enumerable: false, configurable: false}

```

#### 访问器属性：

configurable,

enumberable,

get: 读取的时候调用，默认undefined,

set：写入时调用，默认undefiend

访问器属性只能通过`Object.defineProperty()`定义


#### 定义对象的多个属性，defineProperities
Object.defienProperities(obj, {
  name: {
    value: '',
    ...
  }
  ...
})

##### 读取属性的描述符，getOwnPropertyDescriptor

若该属性是数据属性，则返回configurable,enumberable,value,writable；

若该属性是访问器属性，则返回configurable,enumberable,get,set

```js
let w = {};
Object.defineProperties(w, {
    a: {
        value: 1,
        configurable: true, 
        enumberable: true, 
        writable: true
    },
    b: {
        get: function() {return this.a;},
        set: function(val) {this.a = val}
    }
})

for(let i in w) {console.log(i, w[i])} // undefiend

Object.getOwnPropertyDescriptor(w, 'a') //{value: 1, writable: true, enumerable: true, configurable: true}

Object.getOwnPropertyDescriptor(w, 'b') // {enumerable: false, configurable: false, get: ƒ, set: ƒ}

w.b // 1,
w.b = 2;
w.a // 2
```

##### object.freeze(obj)
是把可写（writable）可删（configurable）设置为false, 但还是可枚举的，即可访问； 

#### object.seal(obj)
是指密封属性，即只是属性不可删（即configurable为false），当然也不可添加新属性

 ```js
 var m = {a: 1, b: 2};
 var n = {a: 1, b: 2};
 Object.getOwnPropertyDescriptor(m, 'a');
 //{value: 1, writable: true, enumerable: true, configurable: true}
 Object.freeze(m);
 m.a
 //{value: 1, writable: false, enumerable: true, configurable: false}

Object.seal(n);
n.a
//{value: 1, writable: true, enumerable: true, configurable: false}

 ```

### 设计模式

#### 工厂模式

用函数来封装特定接口以创建对象；用以解决创建多个相似对象的问题；
弊端: 无法识别对象类型，即实例无法标识为一种特定类型；

```js
function createPerson(name, age, sex) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.sex = sex;
  o.sayname = function() {
      console.log(this.name);
  }
  return o;
}
let person1 = createPerson('wml', '18', 'female');
let person2 = createPerson('w', '20', 'man');
```

#### 构造函数

使用`new`关键字来调用的函数，成为构造函数。构造函数以大写字母开头。

弊端： 每次创建一个新的实例，每个方法都会在新实例中重新创建一次。

调用构造函数步骤：

1. 创建一个新对象，(创建一个新的内存空间)；
2. 将构造函数的作用域赋给新对象(this指向新对象的内存空间)
3. 执行构造函数中的代码，（为新对象添加属性）
4. 返回新对象。

```js
function Person(name, age, sex) {
  this.name = name;
  this.age = age;
  this.sex = sex;
  this.sayName = function() {
    console.log(this.name);
  }
}

let person1 = createPerson('wml', '18', 'female');
person1.constructor == Person // true
person1 instanceof Person // true
```
构造函数模式胜于工厂模式的地方在于可以将实例标识为一种特定的类型；

构造函数也是函数，除了使用new操作符来创建实例，也可以直接调用，但this将会指向全局变量window(浏览器环境中)；

```js
// 当作普通函数
Person('wml', '18', 'female');
window.sayName(); // wml
window.name; // wml

// 在另一个对象作用域中调用
let o = new Object();
Person.call(o, 'wml', '18', 'female');
console.log(o); // {name: "wml", age: "18", sex: "female", sayName: ƒ}
o instanceof Person; //false
```

#### 原型模式

##### --原型
1、function才有prototype属性，指向一个对象，这个对象包含特定类型实例的方法和属性，即实例的原型对象，可以让实例共享原型的方法和属性。prototype指向原型对象，默认情况下都会有一个constructor（构造函数）属性，这个属性包含一个指向
prototype所在函数的指针，即`func.prototype.constructor === func`;  

2、调用构造函数创建实例后，会有一个指针指向构造函数的原型对象，各大浏览器实现为__proto__;

3、只在原型上添加属性和方法，构造函数成了一个空函数，实例的属性和方法都是共享的。

4、读取对象的某个属性，先从对象实例开始搜索，有则返回，无则向对象的原型上搜索，直到找到该对象返回，无则返回undefined。可以用hasOwnProperty方法判断属性是来自原型还是实例（该方法只能获取到实例属性）；`!obj.hasOwnProperty('属性') && "属性" in obj `可以判断属性来源于原型。

5、所有函数的原型默认都是Object的实例

```js
// 原型
function A() {}
let a = new A();
A.prototype.constructor === A //true
a.__proto__ === A.prototype //true
a.__proto__.__proto__ === A.prototype.__proto__ //true(都是指向原始对象Object)
```

```js
// 原型模式
function Person() {}
Person.prototype.name = 'wml';
Person.prototype.age = 18;
Person.prototype.sayName = function() {
  console.log(this.name);
};

let person1 = new Person();
let person2 = new Person();
person1.name // wml
person2.name // wml
```

弊端： 不能用构造函数初始化传参，所有实例的属性和方法都是共享的。

#### 组合构造函数模式和原型模式

组合模式就是使用构造函数模式定义实例的属性，通过原型模式定义共享的属性和方法

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}
Person.prototype = {
  constructor: Person,
  sayName: function() {
    console.log(this.name);
  }
}
let person1 = new Person('wml', 18);
let person2 = new Person('wml2', 16);
person1.name === person2.age // false
person1.sayName === person1.sayName //true
```

### 继承

#### 原型链

所谓原型链就是原型对象指向另一个原型的实例，另一个原型又指向另一个原型对象的实例，层层递进，这就是原型链的概念。

#### 原型继承

```js
function A() {
    this.namess = 'wml';
}
A.prototype.getName = function() {
    return this.namess;
}
function SubA() {
    this.subName = 'wml2';
}
SubA.prototype = new A();

SubA.prototype.getSubName = function() {
    return this.subName;
}

let ins = new SubA();
SubA.prototype.__proto__ === A.prototype // true
ins.__proto__ === SubA.prototype // true
ins.constructor === A // true（ins的构造函数原本指向SubA,但SubA的prototype指向了A的原型，A的原型的constructor指向A）
ins.namess // wml
ins.getName(); //wml
ins.subName // wml2
ins.getSubName(); // wml2

// 重写超类型中的方法
SubA.prototype.getName = function() {return 're_wml'} 
ins.getName(); //re_wml
```

原型继承的缺点：超类型的原型属性会被所有实例共享(SubA的prototype指向了A的实例，A的原型属性变成了SubA的原型属性)；不能向超类型传参；

```js
ins.namess = 'wml3';
let ins2 = new SubA();
ins2.namess // wml3
```

#### 构造函数继承

本质是子类型的构造函数内部调用超类型的构造函数；

优点：可以每个实例有自己独立的属性；可传参；

缺点：和构造函数一样，方法在构造函数中定义，函数无法复用

```js
function A(name) {
  this.namess = name;
}
function SubA() {
  A.call(this, 'wml');
  this.subName = 'wml2';
}
let ins = new SubA();
ins.namess // wml
ins.namess = 'wml3' // wml3
let ins2 = new SubA()
ins2.namess // wml
```

#### 组合继承

结合原型继承和构造函数继承，使用原型链对原型属性和方法进行继承，使用构造函数对实例属性继承，使之拥有各自的属性。

缺点：会调用两次超类型的构造函数，一次是创建子类原型，一次是在子类构造函数内部调用

```js
function A(name) {
    this.namess = name;
    this.color = [1,2];
}
A.prototype.getName = function() {
    return this.namess;
}
function SubA(name) {
    A.call(this, name); //第二次调用A()
    this.subName = 'wml2';
}
SubA.prototype = new A(); // 第一次调用A()
SubA.prototype.constructor = SubA;

SubA.prototype.getSubName = function() {
    return this.subName;
}

let ins = new SubA('wml');
ins.__proto__ === SubA.prototype // true
ins.__proto__ === SubA.prototype // false
ins.namess // wml
ins.color.push(3) // [1,2,3]
let ins2 = new SubA('wml3')
ins2.namess // wml3
ins2.color //[1,2]
```

#### 寄生组合式继承

通过构造函数继承属性，通过原型链的混成式继承方法

```js
// 原型式继承
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
// 寄生组合基本模式
// 本质： 拷贝了一个超类型原型的副本
function inheritPrototype(sub, sup) {
  let pro = object(sup.prototype); //创建对象
  pro.constructor = sub; //增强对象 
  sub.protototype = pro; //指定对象 
}

function A(name) {
  this.namess = name;
  this.color = [1,2];
}
A.prototype.getName = function() {
  return this.namess;
}
function SubA(name, subName) {
  A.call(this, name)
  this.subName = subName;
}
inheritPrototype(SubA, A);
SubA.prototype.getSubName = function() {
  return this.subName;
}
let ins = new SubA('wml', 'wml2')
```