---
title: js 实现一个debounce防抖函数
date: 2020-05-23 17:00:00
tags:
  - JavaScript
---

debounce是指当函数被触发时，如果没有到我们设定的时间，函数不会执行，如果在设定时间之前多次触发，那么计时器会被重设，直到最后一次触发后过了设定的时间，函数才会真正执行。

一般的应用场景是：input组件根据用户输入去做一些请求，比如联想词这种需求，这个时候我们尽量不要每当input的change事件触发就去请求，我们可以等用户停止输入后比如100ms再去请求。

具体场景其实可以根据这个函数的实际功能然后结合业务需求去考虑是否使用。

我们来看看如何实现：
```javascript
function debounce(fn, delay) {
  let timer
  return function(...args) {
    if (timer) clearTimeout(timer)
    // 使用箭头函数来处理this问题
    timer = setTimeout(() => fn.apply(this, args), delay)
  }
}
```

这样，一个基础版的防抖函数就写好了。

然后我们再来优化一下，实际使用中，我们可能需要让它第一次立即执行，然后后面延迟执行，这个需要怎么实现呢，我们来看一下：
```javascript
function debounce(fn, delay, immediate) {
  let timer
  let result
  return function(...args) {
    if (timer) clearTimeout(timer)

    if (immediate) {
      // 如果timer存在，说明第二次调用的时候还没到delay时间，因为如果超过delay时间
      // timer会被赋值为null，所以这个时候我们不应该执行fn，应该重新设置一个定时器
      // 但如果是一次的时候，因为还没有设过定时器，所以这里timer会是undefined
      if (timer) {
        timer = setTimeout(() => timer = null, delay)
      } else {
        result = fn.apply(this, args)
        return result
      }
    } else {
      timer = setTimeout(() => fn.apply(this, args), delay)
    }
  }
}
```

实际的比如lodash.debounce会实现的比这个复杂一些，因为要考虑到很多种情况，但是基本的思想都在这里了。