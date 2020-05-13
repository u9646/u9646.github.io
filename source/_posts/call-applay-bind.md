---
title: 如何模拟实现一个call、apply、bind函数
date: 2020-05-13 18:34:23
tags:
  - JavaScript
---

首先强烈推荐这位大佬的文章，写的相当棒，后续讨论内容的也是你来我往，分析的很透彻。
[JavaScript深入之call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)
[JavaScript深入之bind的模拟实现](https://github.com/mqyqingfeng/Blog/issues/12)

call, apply, bind这三个方法都可以改变函数内部this指向。区别是call, apply是立即指向该函数，而bind是返回一个新的函数，用于下次调用。

其中，call和apply的区别是传递函数参数的方式不同，call是一个一个传入，例如call(this, arg1, arg2, arg3)这样。但是apply是通过一个数组传递，比如apply(this, [arg1, arg2, arg3])。

### call模拟实现

首先我们实现绑定this功能。
```
// 比如我们有一个foo函数
function getName() {
  return this.name;
}

// 还有一个wechat对象
const wechat = { name: 'fedaily' };

// 我们希望实现
getName.call(wechat); // fedaily
```

那么在call内部如何设计能够实现这个功能呢，我们可以联想到将`getName`方法变成`wechat`对象里的一个方法，然后通过`wechat.getName()`，是不是就可以实现这个效果了。

我们来实践一下：
```
Function.prototype.call = function (context) {
  context.fn = this;
  context.fn();
  delete context.fn;
}
```

以`wechat`和`getName`这个为例，这里的this即getName，context即wechat。
我们将`getName`赋值给`wechat`对象的`fn`熟悉，然后通过`wechat`对象调用，最后删除这个fn属性。（实际中我们肯定不能用fn这个名字，避免和对象原本重复，我们可以用Symbol实现）

然后我们绑定this的功能就实现。还有传递参数，这个也好办。
```
Function.prototype.call = function () {
  const [ctx, ...args] = arguments;
  ctx.fn = this;
  ctx.fn(...args);
  delete ctx.fn;
}
// 这里其实用了ES6的语法，但是主要为了更好的说明整个实现过程，理解原理就好
```

这里通过`arguments`对象去获取传进来的参数以及`this`。

然后考虑到函数可能是有返回值的，所以`ctx.fn()`的执行结果也需要返回。同时this可能为null，那么我们需要将this指向window。

所以我们再来调整一下：
```
Function.prototype.call = function () {
  const [ctx, ...args] = arguments;
  ctx.fn = this || window;
  const result = ctx.fn(...args);
  delete ctx.fn;
  return result;
}
```

这样，我们就模拟实现了一个call方法。

### apply模拟实现

接上文，我们再来实现一个apply就很容易了。我们直接上代码：
```
Function.prototype.apply = function () {
  const [ctx, args] = arguments; // args区别
  ctx.fn = this || window;
  const result = ctx.fn(...args);
  delete ctx.fn;
  return result;
}
```

### bind模拟实现

bind会返回一个新函数，新函数执行时的this是bind方法的第一个参数。

根据这些特征，我们可以想到使用apply帮助实现。还是以上面的`getName`函数为例。
```
Function.prototype.bind = function () {
  const [ctx, ...args] = arguments;
  const self = this; // 这里的this即getName函数
  return function () {
    self.applay(ctx, args);
  }
}
```

这样，绑定this的功能我们就实现了。然后bind其实是可以传递参数的，bind返回的函数调用的时候也是可以再传递参数的，同时调用bind的方法可能是有返回值的，所以我们处理一下
```
Function.prototype.bind = function () {
  const [ctx, ...args] = arguments;
  const self = this; // 这里的this即getName函数
  return function () {
    // 这下面的arguments其实是调用bind返回的函数，当这个函数调用时传递的参数
    // 同时返回函数执行结果
    return self.applay(ctx, args.concat(arguments));
  }
}
```

bind函数还有一个特性，就是bind返回的函数是可以作为构造函数的，当它作为构造函数时，它之前绑定的this会被忽略。
为了保证bind返回的函数能够继承到调用函数的原型(即getName的原型)。所以我们需要修改bind返回函数的原型为this的原型(即getName的原型)。

我们再来尝试一下：
```
Function.prototype.bind = function () {
  const [ctx, args] = arguments;
  const self = this;
  const fBond = function () {
    // 注意这里的this不是getName了，而是调用bind之后方法的this
    // const newGetName = getName.bind(this)
    // const n = new newGetName()
    // this就是n
    return self.apply(this instanceof fBond ? this : ctx, args.concat(arguments));
  }
  fBond.prototype = self.prototype;
  return fBond;
}
```

然后为了不会修改原来函数的原型，即getName的原型。我们可以通过一个中间函数来继承。

```
Function.prototype.bind = function () {
  if (typeof this !== "function") {
    throw new Error("Must be function to call bind");
  }

  const [ctx, args] = arguments;
  const self = this;
  var fNOP = function () {};
  const fBond = function () {
    return self.apply(this instanceof fBond ? this : ctx, args.concat(arguments));
  }
  // 这里将self的原型赋值给来fNOP，后面fBond的原型赋值为new fNOP()，这样就和self的原型断开了
  // 后面再修改fBond的原型也不会影响到self，即getName.prototype
  fNOP.prototype = self.prototype;
  fBond.prototype = new fNOP();
  return fBond;
}
```

这样，我们就实现了一个比较完整的bind方法了。

### 前端收藏家（微信号: fedaily）
##### 收集全网优秀前端技术资讯，与你分享，共同成长。
