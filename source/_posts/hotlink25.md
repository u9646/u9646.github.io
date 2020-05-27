---
title: 第25期 深入解析 EventLoop 和浏览器渲染、帧动画、空闲回调的关系 & 精读《@umijs/use-request》源码
date: 2020-05-23 09:40:59
tags:
  - 精选
---

[为什么你的网页需要 CSP?](https://mp.weixin.qq.com/s/QAuTBQUAc3K9ru1mPtCPbA)

CSP 的主要好处就是可以全面禁止使用不安全的嵌入式 JavaScript。内联 JavaScript（无论是反射的还是存储的），意味着不正确的转义用户输入都可以被 Web 浏览器解释为 JavaScript 代码。通过使用 CSP 禁用嵌入式 JavaScript，你可以有效消除针对你站点的几乎所有 XSS 攻击。

[Lighthouse 6.0 新功能](https://mp.weixin.qq.com/s/7Q_CTnOkXGH_8QCDhmvh1A)

5月19日，Lighthouse 发布了 6.0 版本，带来了非常多的新特性，让我们一起来了解一下吧！

[精读《@umijs/use-request》源码](https://mp.weixin.qq.com/s/ZbIiwSTurQXw6qu2rJwkJA)

与组件生命周期绑定的 Utils 非常适合基于 React Hooks 来做，比如可以将 “发请求” 这个功能与组件生命周期绑定，实现一些便捷的功能。

这次以 @umijs/use-request 为例子，分析其功能思路与源码。

[深入解析 EventLoop 和浏览器渲染、帧动画、空闲回调的关系](https://mp.weixin.qq.com/s/Bh-nveCyLqkEDGQXri-8fg)

关于 Event Loop 的文章很多，但是有很多只是在讲「宏任务」、「微任务」，我先提出几个问题：

* 每一轮 Event Loop 都会伴随着渲染吗？
* requestAnimationFrame 在哪个阶段执行，在渲染前还是后？在 microTask 的前还是后？
* requestIdleCallback 在哪个阶段执行？如何去执行？在渲染前还是后？在 microTask 的前还是后？
* resize、scroll 这些事件是何时去派发的。

[node_modules 困境](https://mp.weixin.qq.com/s/uJeRnCOqsg1QVXbZwYzWAw)

Ryan 对于 node.js 的十大遗憾之一就是支持了 node_modules，node_modules 的设计虽然能满足大部分的场景，但是其仍然存在着种种缺陷，尤其在前端工程化领域，造成了不少的问题，本文总结下其存在的一些问题，和可能的改进方式。

### 公众号: 前端收藏家。收集全网优秀前端技术资讯，与你分享，共同成长。