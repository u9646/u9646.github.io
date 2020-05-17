---
title: 彻底弄清js继承的几种实现方式
date: 2020-05-17 17:31:07
tags:
  - JavaScript
---

js有几种经典的继承方式。比如原型链继承、构造函数继承、组合继承、寄生组合继承、ES6继承。让我们一一分析并实现。同时了解每种方案的优缺点。

其实js的继承本质上是通过原型链机制实现的扩展。不管是哪种继承方式，都是通过操作父类原型链和子类原型链形成关联关系实现的。只是不同实现中需要考虑不同的问题。在实际项目开发中，建议尽可能使用ES6的class extends实现。
其他实现方式主要是理解背后的原理和思想。

### 原型链继承
原型链继承通过修改子类的原型为父类的实例，从而实现子类可以访问到父类构造函数以及原型上的属性或者方法。

让我们来实现一下：
```javascript
function Parent() {
  this.name = 'fedaily'
}

Parent.prototype.getName = function() {
  return this.name;
}

function Child() {}

// 这里也可以直接写出Child.prototype = Parent.prototype
// 但是这样就不能访问到父类的构造函数的属性了，即this.name
Child.prototype = new Parent()

var child = new Child()
child.getName() // fedaily
```

#### 优点
实现逻辑简单

#### 缺点
父类构造函数中的引用类型（比如对象/数组），会被所有子类实例共享。其中一个子类实例进行修改，会导致所有其他子类实例的这个值都会改变

### 构造函数继承
构造函数继承其实就是通过修改父类构造函数this实现的继承。我们在子类构造函数中执行父类构造函数，同时修改父类构造函数的this为子类的this。

我们直接看如何实现：
```javascript
function Parent() {
  this.name = ['fedaily']
}

function Child() {
  Parent.call(this)
}

var child = new Child()
child.name.push('fe')

var child2 = new Child() // child2.name === ['fedaily']
```

#### 优点
解决了原型链继承中构造函数引用类型共享的问题，同时可以向构造函数传参（通过call传参）

#### 缺点
所有方法都定义在构造函数中，每次都需要重新创建（对比原型链继承的方式，方法直接写在原型上，子类创建时不需要重新创建方法）

### 组合继承
同时结合原型链继承、构造函数继承就是组合继承了。
```javascript
function Parent() {
  this.name = 'fedaily'
}

Parent.prototype.getName = function() {
  return this.name
}

function Child() {
  Parent.call(this)
  this.topic = 'fe'
}

Child.prototype = new Parent()
// 需要重新设置子类的constructor，Child.prototype = new Parent()相当于子类的原型对象完全被覆盖了
Child.prototype.constructor = Child
```

#### 优点
同时解决了构造函数引用类型的问题，同时避免了方法会被创建多次的问题

#### 缺点
父类构造函数被调用了两次。同时子类实例以及子类原型对象上都会存在name属性。虽然根据原型链机制，并不会访问到原型对象上的同名属性，但总归是不美。

### 寄生组合继承
寄生组合继承其实就是在组合继承的基础上，解决了父类构造函数调用两次的问题。我们来看下如何解决的：
```javascript
function Parent() {
  this.name = 'fedaily'
}

Parent.prototype.getName = function() {
  return this.name
}

function Child() {
  Parent.call(this)
  this.topic = 'fe'
}

// 仔细看这个函数的实现
inherit(Child, Parent)
```

```javascript
function inherit(child, parent) {
  var prototype = object(parent.prototype)
  prototype.constructor = child
  child.prototype = prototype
}

// 这个函数的作用可以理解为复制了一份父类的原型对象
// 如果直接将子类的原型对象赋值为父类原型对象
// 那么修改子类原型对象其实就相当于修改了父类的原型对象
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
```

#### 优点
这种方式就解决了组合继承中的构造函数调用两次，构造函数引用类型共享，以及原型对象上存在多余属性的问题。是推荐的最合理实现方式（排除ES6的class extends继承哈哈哈）。

#### 缺点
没有啥特别的缺点

### ES6继承
ES6提供了class语法糖，同时提供了extends用于实现类的继承。这也是项目开发中推荐使用的方式。

使用class继承很简单，也很直观：
```javascript
class Parent {
  constructor() {
    this.name = 'fedaily'
  }

  getName() {
    return this.name
  }
}

class Child extends Parent {
  constructor() {
    // 这里很重要，如果在this.topic = 'fe'后面调用，会导致this为undefined，具体原因可以详细了解ES6的class相关内容，这里不展开说明
    super()
    this.topic = 'fe'
  }
}

const child = new Child()
child.getName() // fedaily
```
