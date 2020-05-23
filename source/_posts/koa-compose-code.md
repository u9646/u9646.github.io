---
title: koa-compose源码解读
date: 2020-05-23 11:54:58
tags:
  - javascript
---

[koa-compose](https://github.com/koajs/compose/blob/master/index.js)

我们先来看下如何使用compose：

```
function async m1(ctx, next) {
  ctx.middleware.m1 = 'm1'
  next()
}
function async m2(ctx, next) {
  ctx.middleware.m2 = 'm2'
  next()
}

const middlewareFunc = componse([m1, m2])
```

当我们执行`middlewareFunc`的时候。首先我们执行m1()，当执行到m1内部的next方法时，这个next其实就是m2。然后再继续执行m2。

那么compose是如何实现这种效果的呢，我们一步一步分解一下，首先我们知道compose会返回一个函数，然后这个返回的函数执行的时候，会依次执行传入的数组中的函数。同时每个函数会接收一个next，这个next指向数组中下一个函数。

我们大概实现一版：
```
function compose(funs) {
  return function(context) {
    return funs[0](context, funs[1]);
  }
}
```

当然这个肯定是无法实际使用的，但是我们可以看到确实实现了next的这种特性。而且也无法处理任意长度funs的情况，我们来完善一下：
```
function compose(funs) {
  return function(context) {
    let index = -1
    return dispatch(0)
    function dispatch(i) {
      index = i
      return funs[i](context, funs[i + 1])
    }
  }
}
```

这里我们利用闭包的特性保存来一个index，用这个来决定执行funs中的哪个函数。然后通过每次调用将index + 1实现m1调用的时候next是下一个函数也就是m2。

其实这个就是compose的整个思想了，当然实际的koa-compose是返回的promise，同时考虑了一些异常处理，我们来看下最终的实现:
```
function compose (middleware) {
  // 首先我们判断middleware是不是数组，以及数组中每个元素是不是function
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }

  return function (context, next) {
    // index初始值为-1，因为第一次调用下标是0，同时需要判断index是否大于i，如果为0会和下面判断逻辑冲突
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```