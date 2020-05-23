---
title: js 实现一个throttle节流函数
date: 2020-05-23 17:43:22
tags:
  - JavaScript
---

节流是指每隔一段时间，我们只执行一次动作。

一般用于监听滚动事件等情况。当我们某个动作执行的很频繁，需要限制它执行的频率的时候，就可以使用节流函数。

根据这些概念，我们先来实现一版看看原理：
```javascript
function throttle(fn, delay) {
  let prevTime = 0
  return function(...args) {
    const now = new Date().getTime()
    if (now - prevTime > delay) {
      fn.apply(this, args)
      prevTimer = now
    }
  }
}
```

这种实现有一个问题就是最后一次动作会不执行，假设delay是1s，当我们第1.5s就不再触发的时候，实际上，我们只是在第0s，也就是第一次触发时候执行来，然后在第1s时候又执行了一次，但是我们其实是1.5s才停止的。

所以有时候根据需要，我们其实也需要在最后一次的时候执行一次动作，那么怎么实现呢，可以借助于setTimeout来实现：
```javascript
function throttle(fn, delay) {
  let prevTime = 0
  let timer
  return function(...args) {
    const now = Date.now()
    const remaining = delay - (now - prevTime);
    // 如果第二次执行超过delay了就立即执行
    // remaining > wait是为了处理修改了系统时间的情况
    // 比如把当前系统时间往过去调了十分钟，那么now其实是比prevTime小的
    // 那么remaning就可能是一个比较大的数了
    if (remaining > 0 || remaining > wait) {
      if (timer) {
        clearTimeout(timer)
        timer = null
      }
      fn.apply(this, args)
      prevTimer = now
    } else if (!timer) {
      // 这里就是为了处理1.5s停止触发的情况
      // 以1.5s为例，delay为1s，这个时候我们设置一个定时器
      // 让它在0.5s后执行
      // 这样在整个过程中，就是第0s, 第1s，第2s分别执行一次，共三次
      // 虽然我们是1.5s就停止触发了
      // 这样就保证了最后一次动作一直可以执行
      timer = setTimeout(() => {
        fn.apply(this, args)
        timer = null
        prevTime = Date.now()
      }, remaining)
    }
  }
}
```