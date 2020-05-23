---
title: 3年前端面试总结
date: 2020-05-23 10:34:56
tags:
  - 面试
---

> 持续更新中...

### javascript基础

#### 合并两个数组
```
// 方法1: 使用concat, concat返回一个新数组，同时并不修改原数组
const a1 = [1]
const a2 = [2]
const a3 = a1.concat(a2) // [1,2]

// 方法2: 使用扩展运算法
const a4 = [...a1, ...a2] // [1,2]

// 其他方法: for循环遍历
```

#### 合并两个对象
```
// 方法1: Object.assign
// ⚠️该方法只复制值，如果对象属性值是一个引用类型，那么就会复制引用地址，也就是说会存在引用共享的问题
const o1 = { name: 'fedaily' }
const o2 = { topic: 'fe' }
const o3 = Object.assign({}, o1, o2)
```

#### 实现koa的compose方法
可以直接看下源码，只有不到50行[koa-compose](https://github.com/koajs/compose/blob/master/index.js)

function compose (middleware) {
  return function (context, next) {
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

#### 实现js new操作符效果
[js new 操作符实际发生了什么](https://juejin.im/post/5ec8e337518825430044946e)

```
function newFactory() {
  var obj = new Object()
  Constructor = [].shift.call(arguments);
  obj.__proto__ = Constructor.prototype;
  // 处理构造函数有返回值的情况
  // 返回值如果是一个object则直接返回，否则返回obj
  var ret = Constructor.apply(obj, arguments);
  return typeof ret === 'object' ? ret : obj;
};
```

#### js继承的几种实现方式

[彻底弄清js继承的几种实现方式](https://juejin.im/post/5ec114095188256d545febbf)

#### 模拟实现call, apply bind

[如何模拟实现一个call、apply、bind函数](https://juejin.im/post/5ebbde296fb9a0433769777a)

#### js实现一个debounce防抖函数

[js 实现一个debounce防抖函数](https://juejin.im/post/5ec8eff4f265da76b4047da4)

#### js实现一个throttle节流函数

[js 实现一个throttle节流函数](https://juejin.im/post/5ec8f771f265da770a6156d1)

### typescript

#### interface 和 type 的区别
* interface会创建一个新的类型名称，可以在任何地方使用。但是type只是一个引用。
* interface支持extends和implements，但是type不支持，不过可以通过交叉类型实现类似extends效果。
* 尽量使用interface代替type，不过如果遇到需要使用联合类型或者元组的时候，我们还是使用type。

https://juejin.im/post/5ec0ff23f265da7bed6b3c85 这里有更详细的解释

### css

#### flex 0 1 auto表示什么意思
是flex-grow(定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大), flex-shrink(定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。), flex-basis(定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小)


### 算法

#### 求字符串数组的最长公共前缀
```
var longestCommonPrefix = function(strs) {
    if (strs === null || strs.length === 0) return "";
    if(strs.length === 1) return strs[0]
    let min = 0, max = 0
    // ⚠️精髓在这里
    // 仔细看，我们并不是比较数组中某一个字符串的长度，而是大小，'s' > 'a' 直接这样比较的
    // 这种比较其实比较的就是字符串每个字符的charCode
    // 比如's'.charCodeAt()是115，而'a'是97，所以's' > 'a' 返回true
    // 这里其实是筛选字符串公共前缀最长和最短的元素
    // 以["flower","flow","flight"]为例，公共前缀都是'fl'
    // 其实最长和最短随便是那个都没有关系，我们根据charCode算出最长或者最短即可
    // 我们再看，以["flower","flow","slight"]为例，公共前缀都是''
    // for循环结束后，strs[max]其实是'slight'，因为s比f的charCode大，所以'slight'就比f开头的所有字符串大
    // strs[min]其实就是'flow'，因为'flower'比它多了e。
    // 知道strs[min] strs[max]之后，以min为模板，去比较和max有多少重复的就可以了
    for(let i = 1; i < strlength; i++) {
        if(strs[min] > strs[i]) min = i
        if(strs[max] < strs[i]) max = i
    }
    for(let j = 0; j < strs[min].length; j++) {
        if(strs[min].charAt(j) !== strs[max].charAt(j)) {
            return strs[min].substring(0, j)
        }
    }
    return strs[min]
};
```