---
layout:     post
title:      "【读书笔记】设计模式"
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
