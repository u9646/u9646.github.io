---
title: 第19期 彻底搞懂React源码调度原理（Concurrent模式）& HTTP/3原理与实践
date: 2020-05-15 09:17:13
tags:
  - 精选
---

[HTTP/3原理与实践](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651236564&idx=1&sn=4cfbf59ed50cc45d889613e5b19292bd&scene=21#wechat_redirect)

2015 年 HTTP/2 标准发表后，大多数主流浏览器也于当年年底支持该标准。此后，凭借着多路复用、头部压缩、服务器推送等优势，HTTP/2 得到了越来越多开发者的青睐。不知不觉的 HTTP 已经发展到了第三代，鹅厂也紧跟技术潮流，很多项目也在逐渐使用 HTTP/3。本文基于 QQ兴趣部落接入 HTTP/3 的实践，聊一聊 HTTP/3 的原理以及业务接入的方式。

[如何回答好这个高频面试题：CommonJS和ES6模块的区别？](https://mp.weixin.qq.com/s?__biz=MzI1ODk2Mjk0Nw==&mid=2247485426&idx=1&sn=1caa7a95942fc573ae15b004010522cd&scene=21#wechat_redirect)

通过阅读本篇文章你可以学习到：
原始模拟模块的一些写法
CommonJS规范
AMD规范
CMD规范
AMD和CMD的区别
ES6 Modules规范
CommonJS与ES6 Modules规范的区别

[那些年与面试官交手过的HTTP问题](https://mp.weixin.qq.com/s?__biz=MzA4ODUzNTE2Nw==&mid=2451047001&idx=1&sn=834dd557bfd7a17474160e1b9dabaa61&scene=21#wechat_redirect)

从淡黄的长裙和蓬松的头发我察觉到，面前坐着的这位女面试官属实是有点东西。我的自我介绍也变得声情并茂起来。Skr~~~ 在此期间，小姐姐面无改色的看着我的简历。不过无所谓，这些都不重要。

[彻底搞懂React源码调度原理（Concurrent模式）](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651236611&idx=1&sn=c908931b15b7981e0c52e972a9e6ed07&scene=21#wechat_redirect)

最早之前，React还没有用fiber重写，那个时候对React调度模块就有好奇。而现在的调度模块对于之前没研究过它的我来说更是带有一层神秘的色彩，色彩中朦胧浮现出两个字：“困难”。

### 前端收藏家（微信号: fedaily）
##### 收集全网优秀前端技术资讯，与你分享，共同成长。
