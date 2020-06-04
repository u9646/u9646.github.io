---
title: 一句话说清WeakMap的弱引用
date: 2020-06-02 21:25:20
tags:
  - JavaScript
---

WeakMap的引用是弱引用，gc的时候会忽略这种引用。即如果一个对象只有这种弱引用的时候，
会直接被垃圾回收掉。

我们用代码看下和Map的差别，
```
let obj = { name: 'fedaily' }
const map = new Map()
map.set('account', obj)
map.get('account') // { name: 'fedaily' }

obj = null // 这里将obj置为null
map.get('account') // 这里其实obj值还在 { name: 'fedaily' }
```

从Map这个例子可以看出来，obj被map一直引用着，那么垃圾回收器处理时认为{ name: 'fedaily' }还有其他引用，就不会回收它。如果需要彻底删除它，需要map.delete('account')。

我们再来看下WeakMap:
```
let obj = { name: 'fedaily' }
const weakmap = new WeakMap()
weakmap.set(obj, 'account') // WeakMap的key必须是对象，具体用法这里不展开，可以看MDN上的介绍
weakmap.get(obj) // { name: 'fedaily' }

obj = null // 这里将obj置为null
weakmap.get('account') // undefined 这里就没有了
```

这里当我们将obj置为null的时候，obj整个就被垃圾回收了。包括weakmap里面保存的值。

通过这两个对比，我们很容易理解WeakMap描述的对键对象的引用是弱引用的含义。

WeakMap还有一个特性就是无法遍历所有的key。是这种弱引用特性导致的。

还是以上面那个例子说明，当obj=null的时候，如果垃圾回收器没有执行，那么这个时候weakmap.get(obj)其实是有值的，但如果垃圾回收器执行过了，你再访问weakmap.get(obj)就是返回undefined了。

所以这就导致了WeakMap无法遍历所有key的问题。

希望这些可以帮你理解掌握该在什么时候用WeakMap，什么时候用Map。

**公众号: 前端收藏家。只收藏好的，只推荐好的**