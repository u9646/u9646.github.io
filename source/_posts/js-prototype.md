---
title: JavaScript 原型、原型链概念理解
date: 2020-04-12 16:07:17
tags:
 - JavaScript
---

![](https://github.com/u9646/assets/blob/master/public/WeChatee3b7f703390054a53e5e450712f665f.png?raw=true)

## 原型
让我们从一张图开始。

首先，我们看`Person`这个构造函数。它就是一个普通的函数：
```
function Person () {}
```

然后通过`new`操作符，我们可以生成一个实例：
```
var person = new Person()
```

在我们定义了`Person`这个构造函数的时候，其实还生成了一个对象，这个对象就是原型对象。也就是图中的`Person.prototype`。它就是一个普通的对象。如果我们在控制台打印出来是这样的：
```
{
  constructor: ƒ Person()
  __proto__: Object
}
```

它有一个`constructor`执行其构造函数。构造函数可以通过prototype属性访问原型。

当我们使用`new`操作符创建了一个实例`person`的时候，通过还将这个实例同其构造函数的原型通过`__proto__`关联起来了。

这就是构造函数、原型、实例三者的关系。也就是原型的概念。

## 原型链

从上面我们大概了解了原型的概念。那么什么是原型链呢？

我们看`Person.prototype`。上面我们讲到，原型对象其实就是一个普通的对象，它有一个`constructor`属性指向其构造函数。它还有一个属性`__proto__`指向的是这个对象的原型。

我们知道对象可以通过`Object`构造函数生成。就如同通过`Person`new一个`person`的实例，这个实例有一个属性`__proto__`指向`Person.prototype`一样。

原型对象也是通过构造函数生成的。即`Object`。所以它也有一个`__proto__`属性指向其构造函数的原型，也就是`Object.prototype`。

然后`Object.prototype`其实也是一个普通对象，只是它相比`Person.prototype`。它上面还有一些其他属性，我们打印一下看看：
```
{
  constructor: ƒ Object()
  __defineGetter__: ƒ __defineGetter__()
  __defineSetter__: ƒ __defineSetter__()
  hasOwnProperty: ƒ hasOwnProperty()
  __lookupGetter__: ƒ __lookupGetter__()
  __lookupSetter__: ƒ __lookupSetter__()
  isPrototypeOf: ƒ isPrototypeOf()
  propertyIsEnumerable: ƒ propertyIsEnumerable()
  toString: ƒ toString()
  valueOf: ƒ valueOf()
  toLocaleString: ƒ toLocaleString()
  get __proto__: ƒ __proto__()
  set __proto__: ƒ __proto__()
}
```

我们可以看到，它这里定义了`__proto__`属性的`getter/setter`。

那么`Object.prototype.__proto__`是什么呢？通过控制台我们可以看到返回的是：`null`。

到这里，我们其实以及可以看到，我们通过`Person`构造出来的`person`实例对象，通过`__proto__`属性，其实和`Object`关联了起来。

而这样一个链条我们就称之为原型链。就是图中蓝色的线。

那么原型链的作用是什么呢？

我们还是通过上面的`person`实例来说明。如果我们在控制台尝试`person.toString()`。我们会发现会返回`[object Object]`。这里姑且先不说为什么返回这个，我们其实并没有定义`toString`方法，那么为什么可以调用呢？

其实这里就是利用原型链的机制。当js在执行这句代码的时候，它会尝试在`person`上看有没有`toString`方法，如果没找到会去`Person.prototype`上查找。如果还是没找到，会去`Object.prototype`上查找，上面我们也打印出来，这上面是有`toString`方法的。所以`person.toString()`执行成功了，而不是报错，提示没有`toString`方法。

通过原型链我们可以实现其他面向对象语言的继承的概念。

## Function的原型是什么？
通过控制台，我们可以看到：
```
function anonymous() {}
arguments: (...)
caller: (...)
length: 0
name: ""
constructor: ƒ Function()
apply: ƒ apply()
bind: ƒ bind()
call: ƒ call()
toString: ƒ toString()
Symbol(Symbol.hasInstance): ƒ [Symbol.hasInstance]()
get arguments: ƒ ()
set arguments: ƒ ()
get caller: ƒ ()
set caller: ƒ ()
__proto__: Object
[[FunctionLocation]]: <unknown>
[[Scopes]]: Scopes[0]
```

它其实是一个`anonymous`函数。并不是对一个普通对象。但是它同样有一个`__proto__`属性，指向了其原型，也就是`Object.prototype`。

所有构造函数（除了通过bind,call返回的函数，以及箭头函数）都有一个`__proto__`属性指向`Function.prototype`。

看开始的图，也就是`Person.__proto === Function.prototype`。`Object.__proto === Function.prototype`；

包括其他的内置构造函数，比如`Number`, `String`等。

所以也就有了`Function.__proto__ === Function.prototype`为`true`。这样的情况。我理解是这些其实是js语言内置的原型对象。和`Object.prototype`一样。首先是有了函数的原型，即`Function.prototype`。然后为了`Function`构造函数能够直接访问原型上的方法，所以将其`__proto__`属性指向了函数的原型。

如果你执行`console.log(Number.prototype)`, `console.log(Date.prototype)`。你会发现都有一些区别，Number原型有一些特有函数的实现，Date也是如此。

这些都是js内部实现的一些基础原型对象。
