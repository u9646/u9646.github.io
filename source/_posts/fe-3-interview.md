---
title: 3年前端面试总结
date: 2020-05-23 10:34:56
tags:
  - 面试
---

> 持续更新中...

### 一、javascript基础

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

[koa-compose源码解读](https://juejin.im/post/5ec8f9a2518825430f54b6c7)

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

#### js的闭包理解

根据MDN对闭包的描述，当一个函数和它外部的引用捆绑在一起的时候就形成了闭包。

#### 用 ES5 实现私有变量
可以使用闭包实现：
```javascript
var Tool = (function() {
  var keyboard = 'hhkb'
  return {
    getKeyboard: function() {
      return keyboard;
    }
  }
})()

Tool.getKeyboard() // hhkb
Tool.keyboard // undefined
```

#### 手写Promise.all

// TODO:

#### import 和 require 导入的区别

// TODO:

#### require 有什么性能问题

// TODO:

#### webpack 如何实现动态加载

// TODO:

#### 写一个 promise 重试函数，可以设置时间间隔和次数。function foo(fn, interval, times) {}

// TODO:

#### 实现一个 redux

// TODO:

#### 小程序架构

// TODO:

### 二、typescript

#### interface 和 type 的区别
* interface会创建一个新的类型名称，可以在任何地方使用。但是type只是一个引用。
* interface支持extends和implements，但是type不支持，不过可以通过交叉类型实现类似extends效果。
* 尽量使用interface代替type，不过如果遇到需要使用联合类型或者元组的时候，我们还是使用type。

https://juejin.im/post/5ec0ff23f265da7bed6b3c85 这里有更详细的解释

#### typescript优势
我理解的优势都是静态类型校验带来的，比如稳定性，团队协作，大型项目的维护。完善的类型系统可以让新成员很快的了解项目细节，同时一个完善的类型系统也完全可以充当项目文档。
* 静态类型校验
* 团队协作，类型即文档

#### 实现Pick
```typescript
type Pick<T, K extends keyof T> = { [P in K]: T[P] }
```

### 三、React

#### React事件机制
React使用的合成事件，所有事件都是统一注册到document(一些原生的的事件不是，比如input的invalid事件，应该是因为这个事件只能在当前元素触发)。然后根据事件冒泡机制，统一在document上进行处理，这样可以提高性能，如果每个事件都绑定在真实DOM上，那么创建DOM，更新DOM时还需要处理事件，而且一个元素上如果同时绑定了很多事件，也会影响内存和页面响应。（可以对比事件委托来理解，传统开发模式下，如果需要给ul下面的所有li添加click事件的时候，我们也不会直接在每个li上绑定事件，而是绑定在ul上，然后根据事件冒泡机制来处理）

同时React还在底层对事件做了兼容性处理。

#### React如何做性能优化
一方面从控制render以及减少计算方面入手。
控制render也就减少了React的diff过程，常用不同类型组件的一些手段比如shouleComponentDidUpdate, React.memo。还可以使用useCallback避免函数的更新导致的render，因为函数式组件其实每次都会创建一个新的函数。
减少计算可以通过useMemo缓存计算结果，只在必要的时候计算。

另一方面从减少代码体积方面入手
可以使用React.lazy配合webpack实现组件的懒加载。

#### React的diff策略，React 16和之前版本diff有什么不同

// TODO:

#### React的生命周期的理解，以及16和之前版本区别，为什么有这种区别？

// TODO:

#### React Hooks怎么处理生命周期

// TODO:

#### class 组件与函数式组件的区别

[函数式组件与类组件有何不同？](https://overreacted.io/zh-hans/how-are-function-components-different-from-classes/)

#### React.lazy 的原理是啥

// TODO:

#### react-redux原理

// TODO:

### 四、CSS

#### flex 0 1 auto表示什么意思
是flex-grow(定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大), flex-shrink(定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。), flex-basis(定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小)

#### css优先级
important > 内联 > ID 选择器 > 类选择器 > 标签选择器

### 五、算法

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

#### js实现一个单链表，双向链表

#### 怎么判断单链表相交

#### 怎么找到链表第一个相交节点


### 六、HTTP以及安全相关

#### HTTP缓存

[HTTP缓存](https://juejin.im/post/5ec8e46c518825432c75514b)

#### HTTP三次握手四次挥手

[HTTP三次握手四次挥手](https://juejin.im/post/5ec8e41ce51d45788446825e)

#### xss是什么，如何防范

#### csrf是什么，如何防范

