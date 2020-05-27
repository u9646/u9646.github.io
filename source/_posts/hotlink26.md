---
title: 第26期 详解React 16的Diff策略 & 深入分析Session和Cookie & 图解ChunkSplitPlugin
date: 2020-05-24 20:58:14
tags:
  - 精选
---

[深入分析Session和Cookie](https://mp.weixin.qq.com/s/q83qhK9IfeQmvwjmXOkVyA)

在Web发展史中，我们知道浏览器与服务器间采用的是 http协议，而这种协议是无状态的，所以这就导致了服务器无法知道是谁在浏览网页，但很明显，一些网页需要知道用户的状态，例如登陆，购物车等。

所以为了解决这一问题，先后出现了四种技术，分别是隐藏表单域，URL重写，cookie，session，而用的最多也是比较重要的就是cookie和session了。

[图解ChunkSplitPlugin](https://mp.weixin.qq.com/s/IHJoPUa5G8ENU3lbZBFE7A)

chunk(module 的集合)在 webpack 解析的依赖图中以父子关系联系起来的。最初CommonsChunkPlugin被设计用于 chunk 之间避免重复依赖，但是性能远远不是最优解。

在 webpack 4 中，内置了ChunkSplitPlugin用于替代CommonsChunkPlugin。

以下基于官方 demo进行梳理，将主要的数据结构结合图示和个人的理解进行总结。

揭开此插件的设计思路和源码神秘面纱

[不可错过的实用前端工具](https://mp.weixin.qq.com/s/ISnuV4R1FSrySglXLkyeXQ)

给大家整理了 25 个前端相关的学习网站和一些靠谱的小工具，包括一些小游戏、教程、社区网站和博客，以及一些资源网站，希望可以帮助到大家！

[Deep In React之浅谈 React Fiber 架构(一)](https://mp.weixin.qq.com/s?__biz=MzI1ODk2Mjk0Nw==&mid=2247484469&idx=1&sn=f68d044f1b0e4e2eb981e3878427b75b&scene=21#wechat_redirect)

2016 年都已经透露出来的概念，这都 9102 年了，我才开始写 Fiber 的文章，表示惭愧呀。不过现在好的是关于 Fiber 的资料已经很丰富了，在写文章的时候参考资料比较多，比较容易深刻的理解。

我的思路是自上而下的介绍，先理解整体的 Fiber 架构，然后再细挖每一个点，所以这篇文章主要是谈 Fiber 架构的。

[详解 React 16 的 Diff 策略](https://mp.weixin.qq.com/s?__biz=MzI1ODk2Mjk0Nw==&mid=2247484536&idx=1&sn=94777b8c1aab80dffe1fc224bec02c72&scene=21#wechat_redirect)

我相信在看这篇文章的读者一般都已经了解过 React 16 以前的 Diff 算法了，这个算法也算是 React 跨时代或者说最有影响力的一点了，使 React 在保持了可维护性的基础上性能大大的提高，但 Diff 过程不仅不是免费的，而且对性能影响很大，有时候更新页面的时候往往 Diff 所花的时间 js 运行时间比 Rendering 和 Painting 花费更多的时间，所以我一直传达的观念是 React 或者说框架的意义是为了提高代码的可维护性，而不是为了提高性能的，现在所做的提升性能的操作，只是在可维护性的基础上对性能的优化。

**公众号: 前端收藏家。收集全网优秀前端技术资讯，与你分享，共同成长。**