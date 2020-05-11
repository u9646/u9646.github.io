---
title: 第15期 为新的Facebook.com重建我们的技术栈 & HTTP/3原理与实践
date: 2020-05-11 09:34:38
tags:
  - 精选
---

[HTTP/3原理与实践](https://mp.weixin.qq.com/s?__biz=MzI4NzEyMjUxMA==&mid=2649069145&idx=1&sn=6aab52cf10dbbec2a0d5b4a0385f3780&scene=21#wechat_redirect)

2015 年 HTTP/2 标准发表后，大多数主流浏览器也于当年年底支持该标准。此后，凭借着多路复用、头部压缩、服务器推送等优势，HTTP/2 得到了越来越多开发者的青睐。不知不觉的 HTTP 已经发展到了第三代，鹅厂也紧跟技术潮流，很多项目也在逐渐使用 HTTP/3。本文基于 QQ兴趣部落接入 HTTP/3 的实践，聊一聊 HTTP/3 的原理以及业务接入的方式。

[为新的Facebook.com重建我们的技术栈](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651236491&idx=1&sn=16ce83f02ecb7eefee0b1cb58d971c27&scene=21#wechat_redirect)

当我们考虑如何构建一个新的网络应用—一个为现代浏览器设计的、具有用户对Facebook（我们已知的）所有期望的功能，我们现有的技术栈无法支持我们所需要的类似于桌面应用的感觉和性能。完全重写是非常罕见的，但在这种情况下，由于过去十年来Web技术发生了很多变化，我们知道这是我们实现性能和未来可持续发展目标的唯一途径。今天，我们就分享一下我们在重构Facebook.com时的经验教训，使用React（一种用于构建用户界面的声明式JavaScript库）和Relay（React的GraphQL客户端）来重构Facebook.com。

[HTTP 无状态中的 "状态" 到底指的是什么？](https://mp.weixin.qq.com/s?__biz=MjM5MDc4MzgxNA==&mid=2458454562&idx=1&sn=a5fe5f7f2850119d7ddf6f3ba22f20e5&scene=21#wechat_redirect)

最近在好好了解http，发现对介绍http的第一句话【http协议是无状态的，无连接的】就无法理解了：无状态的【状态】到底指的是什么？

[图解 HTTP 缓存](https://mp.weixin.qq.com/s?__biz=MzI0NTE5NzYyMw==&mid=2247484110&idx=1&sn=23cd33596d6f4a6a6b71b852a1b6c2c6&scene=21#wechat_redirect)

HTTP 的缓存机制，可以说这是前端工程师需要掌握的重要知识点之一。本文将针对 HTTP 缓存整体的流程做一个详细的讲解，争取做到大家读完整篇文章后，对缓存有一个整体的了解。

[面试题：说说事件循环机制(满分答案来了)](https://mp.weixin.qq.com/s?__biz=MzI2NTk2NzUxNg==&mid=2247485017&idx=2&sn=817e8b5d78fe8d46a2d8e62bbe870c50&scene=21#wechat_redirect)

1. 先说基本知识点，宏任务、微任务有哪些
2. 说事件循环机制过程，边说边画图出来
3. 说async/await执行顺序注意，可以把 chrome 的优化，做法其实是违反了规范的，V8 团队的PR这些自信点说出来，显得4. 你很好学，理解得很详细，很透彻。
5. 把node的事件循环也说一下，重复1、2、3点，node中的第3点要说的是node11前后的事件循环变动点。

### 前端收藏家（微信号: fedaily）
##### 收集全网优秀前端技术资讯，与你分享，共同成长。
