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

#### js 函数柯里化
```js
function curry(fn, ...args) {
  if (args.length >= fn.length) {
    return fn(...args);
  }
  return function(...args2) {
    return curry(fn, ...args, ...args2);
  }
}
```

#### js实现一个debounce防抖函数

[js 实现一个debounce防抖函数](https://juejin.im/post/5ec8eff4f265da76b4047da4)

#### js实现一个throttle节流函数

[js 实现一个throttle节流函数](https://juejin.im/post/5ec8f771f265da770a6156d1)

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

#### js基本数据类型
null, undefined, boolean, number, string, symbol, object

#### 数组的常用操作方法以及是否修改原数组
不会修改原数组：concat, entries, every, filter, find, findIndex, forEach, includes, indexOf, join, map, reduce, slice, some, sort, toString
会修改原数组：fill, pop, push, reverse, shift, splice, unshift

#### 什么是闭包？它有什么作用？
闭包就定义在一个函数内部的函数，并且它引用了其外部函数的变量。
主要用于获取函数内部变量以及让变量始终保持在内存中。可以实现一些抽象封装的逻辑，比如私有变量。
闭包会导致变量一直保存在内存中，过量或者使用不当会造成内存泄漏以及性能问题。

#### 什么是箭头函数？和普通函数有什么区别
箭头函数是ES6提供的一种创建函数的新语法。
1. 箭头函数没有自己的this，它的this总是指向外层执行环境的this，它的this在定义时候就已经决定了，并且永远不会改变
2. 箭头函数不能作为构造函数使用，因为它没有自己的this，使用new的时候会报类型错误，提示其不是一个构造函数
3. 箭头函数没有arguments对象，可以使用扩展运算符获取所有参数，比如const foo = (...args) => consle.log(args)
4. 箭头函数没有prototype
5. 箭头函数不能作为generator函数，不能使用yield关键字

#### 如何实现一个深拷贝

[js 如何实现深拷贝deepClone](https://juejin.im/post/5ed3768ef265da77031b6278)

#### script module

浏览器原生支持的模块系统，用法和es6的module类似，通过给script标签添加一个type='module'属性来实现。更高效。

#### 手写Promise.all, Promise.race实现

```js
// Promise.all
Promise.prototype.all = function(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises)) {
      return reject(new TypeError('Promise.all param must be array'))
    }
    let resolvedCount = 0
    const resolvedValues = []
    for(let i = 0; i < promises.length; i++) {
      Promise.resolve(premises[i]).then(value => {
        resolvedCount++
        resolvedValues.push(value)
        if (resolvedCount === promises.length) {
          return resolve(resolvedValues)
        }
      }, error => {
        return reject(error)
      })
    }
  })
}

// Promise.race
Promise.prototype.race = function(promises) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < promiseAry.length; i++) {
      promiseAry[i].then(resolve, reject)
    }
  })
}
```

#### 什么是事件循环？

浏览器环境的事件循环：
主线程会执行所有同步代码，当遇到异步任务时，会将其放到事件队列中，然后继续执行后面的同步代码。一直到主线程执行完所有同步代码。然后再去事件队列中取出任务进行执行。异步任务又分为宏任务和微任务，setTimeout, setInterval属于宏任务，而Promise, MutationObserver属于微任务。主线程执行完同步代码后总是先执行所有的微任务队列，然后再去执行宏任务队列。如果执行事件队列里面的任务时遇到其他异步任务，则继续重复上述操作，一直循环下去。

node的事件循环：
node的事件循环其实是依靠libuv实现的，node事件循环是：外部输入数据->轮询阶段poll->检查阶段check->关闭事件回调阶段close callbacks->定时器执行阶段timers->I/O事件回调阶段I/O callbacks->闲置阶段idle, prepare->轮询阶段这样一个执行过程。

timers阶段会执行定时器回调，比如setTimeout, setInterval, check阶段会执行setImmediate

推荐这篇文章，写得非常好：https://zhuanlan.zhihu.com/p/33058983

#### 宏任务和微任务的区别？
setTimeout, setInterval, requestAnimationFrame, I/O, setImmediate属于宏任务
process.nextTick, MutationObserver, Promise.then catch finally属于微任务

#### 如果在Event Loop中，不断push微任务是否一直会执行?
如果在执行微任务的时候，微任务又有微任务，那么会一直执行下去，不过浏览器有没有做一个限制就不太清楚了。
```
setTimeout(_ => console.log('timeout'))

new Promise((resolve, reject) => {
  console.log(1)
  resolve()
}).then(() => {
  new Promise((resolve, reject) => {
    console.log(2)
    resolve()
  }).then(() => {
    new Promise((resolve, reject) => {
      console.log(3)
      resolve()
    })
  })
})

// 会输出1,2,3,timeout
```

#### import 和 require 导入的区别

import是es6的模块规范，而require是commonjs模块规范；
import是编译时执行，所以必须放在模块顶部，而require是运行时加载，可以在任何地方，像一个函数一样调用。
require使用的是拷贝，针对基础类型，会拷贝值，对于引用类型，会复制其值的引用。但是import不管是基础类型还是引用类型，都是生成一个引用。
当使用require命令加载某个模块时，会执行整个模块，当重复加载某一个模块时，不会再执行该模块，而是取缓存中的值

#### require 有什么性能问题

require是运行时加载，所以会影响性能，而且在浏览器端，因为资源是放在服务端的，那么就会阻塞后面的代码执行。

#### module.exports 和 exports区别
exports是module.exports的一个引用，最终返回的都是module.exports的值，建议一直使用module.exports，因为如果你不小心将exports直接赋值为一个变量，比如exports = xxx，那么这个时候module.exports其实并没有改变，最终返回的也不是你想要的。

#### webpack 如何实现动态加载

主要通过import()实现，webpack遇到import()时，会将这个模块单独打包，然后当浏览器执行到这里时，才去加载对应的资源，然后执行。

#### webpack打包原理

https://juejin.im/post/5e116fce6fb9a047ea7472a6

#### webpack的性能优化如何做

https://juejin.im/post/5b652b036fb9a04fa01d616b

#### webpack的loader和Plugins有什么区别，webpack是如何去使用Plugins的

loader，它是一个转换器，将A文件进行编译成B文件，比如：将A.less转换为A.css，单纯的文件转换过程。

plugin是一个扩展器，它丰富了webpack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务，比如代码压缩等。

#### 写一个 promise 重试函数，可以设置时间间隔和次数。
```js
Promise.retry = function(fn, times, delay){
  return new Promise((resolve, reject) => {
    let retryCount = times
    const action = function() {
      fn().then(res => {
          resolve(res)
      }).catch(err =>{
          if(retryCount <= 0){
            reject(err)
          } else {
            retryCount--
            setTimeout(() => action(), delay)
          }
      })
    }
    action(resolve, reject)
  })
}
```

#### 手写promise的sleep函数实现

const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

#### 实现一个 redux

https://github.com/brickspert/blog/issues/22

#### 小程序架构，小程序的同层渲染原理，小程序的bindtap和catchtap的区别，小程序的通信是怎么样的，小程序的setData的数据如何很大，如何解决, webview组件和原生组件的区别

小程序架构：
小程序是由两个webview去渲染的，一个专门负责UI层渲染，另一个专门复制执行JS，好处就是js无法直接操作DOM，能够解决很多安全问题，更新DOM需要使用微信提供的API，通过这种机制，可以很好的解决安全方面的问题，但同时也带来了一些性能上的问题。

小程序同层渲染原理：
https://developers.weixin.qq.com/community/develop/article/doc/000c4e433707c072c1793e56f5c813

小程序的bindtap和catchtap的区别：
bind不会阻止事件向上冒泡，catch会阻止事件冒泡

小程序的setData的数据如何很大：
可以增量更新，比如一个大的数据其中只有一部分数据更新了，我们不需要全量更新，只更新变化的数据即可


小程序的通信是怎么样的：
利用page的onShow/onHide事件，将数据存到一个公共的storage里面，比如localStorage, globalData，实现通信。或者使用发布订阅模式实现通信。


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
控制render也就减少了React的diff过程，常用不同类型组件的一些手段比如shouleComponentUpdate, React.memo。还可以使用useCallback避免函数的更新导致的render，因为函数式组件其实每次都会创建一个新的函数。
减少计算可以通过useMemo缓存计算结果，只在必要的时候计算。

另一方面从减少代码体积方面入手
可以使用React.lazy配合webpack实现组件的懒加载。

#### React虚拟DOM意义


#### React的diff策略，React 16和之前版本diff有什么不同

[Deep In React 之详谈 React 16 Diff 策略(二)](https://segmentfault.com/a/1190000019918133)

#### React的生命周期的理解，以及16和之前版本区别，为什么有这种区别？

// TODO:

#### React Hooks怎么处理生命周期

React Hooks其实并没有生命周期的概念了，如果非要说想实现之前类似的效果，比如componentDidMount，可以将useEffect的第二个参数设为空数据，即表明这里的回调函数不依赖任何其他参数，只会在组件初始化的时候执行。

#### class 组件与函数式组件的区别

[函数式组件与类组件有何不同？](https://overreacted.io/zh-hans/how-are-function-components-different-from-classes/)

#### React.lazy 的原理是啥

// TODO:

#### React的setState的机制是什么

// TODO

#### 描述一下redux的中间件原理，redux如何进行异步处理

// TODO

#### React hooks的优点

// TODO

#### Dva的同步状态管理，异步状态管理，是如何实现的

### 四、CSS

#### flex 0 1 auto表示什么意思
是flex-grow(定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大), flex-shrink(定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。), flex-basis(定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小)

#### css优先级
important > 内联 > ID 选择器 > 类选择器 > 标签选择器

#### 移动端适配方案

1. 通过meta标签设置viewport为<meta name="viewport" content="width=width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
2. 可以针对不同设备像素比dpr设置不同的样式，也可以动态设置html的font-size，然后使用rem

#### flex的布局了解么，什么是主轴，如何控制主轴和副轴，Flex的优点和缺点

// TODO

#### 如何实现水平垂直居中
```css
.box {
  display: flex;
  jutify-content: center;
  align-items: center;
}

.box {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateX(-50%)
  transform: translateY(-50%)
}
```

#### 什么是BFC，它的作用是什么，什么行为会产生BFC

块级格式化上下文。BFC会建立一个隔离的空间，断绝空间内外元素间相互的作用。当元素的display: flex | inline-flex或者overflow: hidden;或者position: absolute会创建一个BFC。

#### css的动画如何实现, transform有什么好处

可以使用transition, animation等实现，transform可以开启硬件加速，并且不会引起回流重绘

#### 回流和重绘的区别，什么行为会导致回流，什么行为会导致重绘

元素的布局位置发生改变的话会导致回流，元素的颜色等不影响布局的属性发现改变的时候会导致重绘，回流一定会导致重绘，但是重绘不一定会导致回流

当我们使用js获取元素的一些布局属性的时候，比如offsetTop, clientTop, getComputedStyle, getBoundingClientRect等，因为浏览器需要重新执行layout来返回正确的值。

CSS3的transform等动画不会引起回流重绘

#### CSS3里面新增了哪些属性

transition, transform, animation, 选择器，比如:disabled, :last-child, :nth-child
border-radius, background-size, text-shadow, font-face

#### 1px的问题可以如何去解决

ios可以使用0.5px；
使用边框图片
使用box-shadow实现：
```css
box-shadow: 0  -1px 1px -1px #e5e5e5,   //上边线
            1px  0  1px -1px #e5e5e5,   //右边线
            0  1px  1px -1px #e5e5e5,   //下边线
            -1px 0  1px -1px #e5e5e5;   //左边线
```
还可以借助于transform，将1px的通过scale(0.5)缩小一倍

---

### 浏览器

#### 描述一下浏览器页面渲染的过程

浏览器获取到html之后，会开始进行解析，如果遇到css资源，会同步去加载css资源，如果遇到script，会停止解析，知道js执行完毕，因为js可能会改变DOM。（script可以设置async, defer进行异步加载，区别是async加载完会立即执行，这个时候html解析会停止，而defer会在html解析完成之后才执行）。也就是说浏览器会同步处理html和css，然后分别生成一个DOM树，一个CSS树，然后将其合成为一个render树，再遍历渲染树开始计算布局，最后将其绘制到屏幕上。


#### 浏览器白屏是什么导致的

浏览器渲染时候没有请求到或着是请求时间过长导致的。浏览器在执行css,图片等资源时是并发加载的，但是对于js会阻塞html的解析，如果js放在页面顶部也可能会导致白屏。

#### 浏览器存储Cookie、localstorage、sessionStorage的区别

生命周期不一样，cookie可以设置失效时间，不设置的话默认浏览器关闭后失效，localStorage除非手动清除，否则永久保存，sessionStorage仅在当前网页会话中有效，关闭页面或者浏览器后就会被清除。

存储数据大小不一样，cookie在4KB左右，localStorage和sessionStorage是5MB

http请求，cookie会每次都在请求头中，如果cookie过多会导致请求变大，影响性能，localStorage, sessionStorage仅在客户端使用

然后cookie的接口不友好，storage会稍微方便一些

#### cookie如何进行设置的，JS能改变哪些值

通过document.cookie=newCookie设置，可以设置key, value, path, domain, max-age, expires,secure

#### 解决跨域的方法有哪几种，描述一下JSONP的原理，描述一下CORS的过程

1. 配置CORS。
2. 使用JSONP。
JSONP原理：
浏览器端：定义一个函数，假设名为getName，然后创建一个script标签，将其src设置为类似/api?callback=getName的形式。
服务器端：根据请求的callback，返回一个js，这个js是类似这样的：
```
getName(data) // data即我们想要跨域获取的数据
```
因为script加载的js，浏览器会执行，也就执行了getName函数，而这个函数，我们之前已经定义了，所以这个函数内部就可以获取到数据。这就是JSONP跨域的原理。主要是利用了script标签不会有同源策略的限制。实际生产中，还是建议使用CORS的策略。
3. nginx反向代理
4. 使用websocket

#### 进程和线程

进程是程序的一个执行过程，是系统进行资源分配和调度的基本单位。线程是CPU调度和分配的基本单位，一个进程中的多个线程可以共享进程的资源。

#### web性能优化

1. 压缩资源体积，可以通过webpack压缩js/css/html等资源，对于图片进行压缩，选择合适的图片格式，比如不需要透明通道的话应该使用jpg代替png
2. 减少请求数量，可以对打包出来的js/css进行一个合理的拆分，不要让单个文件体积过大，也不要拆分的太碎，那样会增加请求数量，对于图标类，尽量使用svg代替图片。同时可以做动态加载，每次只加载必须的资源，对于图片可以做一个懒加载，在显示的时候才去请求资源
3. 优化资源下载速度，将静态资源部署在CDN上，合理的运用浏览器预加载
4. 增加缓存，可以利用http缓存相关资源，或者使用service worker，localStorage等手段缓存
6. 开发过程中，注意回流和重绘的问题，做一些动画等功能合理运用硬件加速的性能

#### 如何进行首屏加载优化

---

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

#### 寻找最长不重复子串

#### js实现一个单链表，双向链表

#### 怎么判断单链表相交

#### 怎么找到链表第一个相交节点

#### 实现一个sum方法【sum(1, 2, 3)(4)，sum(1, 2)(3, 4)类似的调用】，同时console.log(sum(1, 2, 3)(4) => //输出10

#### 给定一个先增后降的数组，找出其中的最大值

#### 如何实现两个有序数组的合并，要求时间复杂度是O(n)

### 六、HTTP以及安全相关

#### HTTP缓存

[HTTP缓存](https://juejin.im/post/5ec8e46c518825432c75514b)

#### HTTP三次握手四次挥手

[HTTP三次握手四次挥手](https://juejin.im/post/5ec8e41ce51d45788446825e)

#### xss是什么，如何防范

跨站脚本攻击，当我们什么都不处理的时候，假设有人在评论里面输入来一段js脚本，那么当页面渲染时，会将这个当成一个脚本来执行，也就产生了XSS。危险程度还是非常高的。
如何防范：
从输入输出源进行控制，同时返回头中设置x-xss-protection: 1; mode=block，让浏览器启用xss过滤，如果检测到攻击，就阻止页面加载。当页面需要渲染富文本时，建议使用成熟的xss组件。

对url要进行encode

设置CSP，限制非白名单脚本的执行

#### csrf是什么，如何防范

跨站请求伪造，攻击过程是这样的：
当你在A页面登录之后，来到了B页面，这个页面会发起一个A页面的请求，这个时候请求虽然是在B页面发出的，但是浏览器还是会带上A页面登录态等cookie，如果没有任何其他防范措施，这个请求是可以成功的。
如何解决呢：
由于请求是在B页面发出的，那么请求的referer其实是B页面，而不是A页面，可以通过校验referer来判断，不过因为部分浏览器下referer可能会被修改，所以这种方式其实是不可靠的
另一种方式就是加一个token，当你登录成功后，后端通过cookie返回一个token，然后前端每次请求都从cookie里面获取这个token，然后添加到请求头上，因为B页面只能发起请求，没法控制这个请求头，所以这里发出的请求是没有token那个请求头的，后端校验的时候看没有这个请求头，就会拒绝这个请求，从而进行防范

#### 其他安全问题

1. 点击劫持，主要是因为没有限制iframe加载导致的，可以将你的服务请求头设置为x-frame-options为sameorigin，或者通过CSP设置frame-ancestor为白名单，限制其他服务使用iframe去加载你的网站

2. 开启HSTS头，Strict-Transport-Security: max-age=xxxx;includeSubDomains，禁止浏览器使用http方式加载网址，当我们访问http的网址时，可能会被重定向到https的，但是由于http可能被劫持，可能会被重定向到一个恶意网址。但是第一次访问的时候，HSTS并不能生效，浏览器还未收到HSTS。解决方案是浏览器预置HSTS域名列表，二是将HSTS信息加入到域名系统中。

3. window.opener，默认使用a标签或者通过window.open('http://xxx')打开一个新页面的时候，新页面可以通过window.opener获取到前一个页面的window对象，然后就可以进行一些操作，比如将你导航到一个伪造的页面。针对a标签，应该加上rel="noopener noreferrer nofollow"，一般eslint都会有这个提示，window.open可以const newTab = window.open(); newTab.opener = null来控制。

4. CDN 劫持，现在前端基本会将静态资源存储在CDN上，如果CDN被劫持了，那么攻击者就可以纂改我们的前端页面。可以通过SRI，即子资源完整性校验来防范。script和link标签可以设置integrity属性，这个属性是资源的一个哈希值，浏览器可以通过获取文件的哈希值和你提供的哈希值来判断资源是否被纂改。

#### HTTP1.1/HTTP2.0的区别，http3

HTTP2升级：
1. 协议解析基于二进制格式
2. 多路复用。一个连接上可以有多个请求，每个请求可以混杂在一起，接收方可以根据请求id进行归类
3. header压缩。同时使用缓存，避免每次都传输一份完整的头
4. 服务端推送

HTTP1.1的多路复用和HTTP2区别：
HTTP1.1是使用排队的方式，前面的会阻塞后面的，HTTP2可以并行执行，不会影响其他请求

---

### node

#### node内存泄漏的原因

---

### 项目

#### 项目中遇到的问题，如何解决的

#### 项目中还有什么需要改进的地方

#### 在公司有啥技术贡献

#### 自己最大的成长是什么

#### 自己觉得在原公司的开发流程上，还有哪些可以改进的

#### 自己未来的职业规划是怎样的

#### 平时如何进行学习的，目前在学习什么【慎重回答，这是面试的扩展点】