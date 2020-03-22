---
title: 技术精选 - 第2期
date: 2020-03-15 14:50:12
tags:
  - 精选
---

[具有“脚本魔力”的属性们](https://mp.weixin.qq.com/s/8znEKWxCahJE1zSf9Zky_Q)
属性的“脚本魔力”，是指仅仅通过一行简单的属性设置，就能实现看起来要 JS 才能完成的交互功能。包括但不限于

[完全理解 redux（从零实现一个 redux）](https://mp.weixin.qq.com/s/jfDrhX6jMr36dRaFCH9Xbw)
记得开始接触 react 技术栈的时候，最难理解的地方就是 redux。全是新名词：reducer、store、dispatch、middleware 等等，我就理解 state 一个名词。

[用 Service Worker 实现前端性能优化](https://mp.weixin.qq.com/s/SLG_cDxDo7BaoQqAyLGa-Q)
说起前端性能优化, 我们首先想到的可能就是用 Gulp 、Webpack 之类的自动化构建工具对 HTML、CSS 、JS 代码进行压缩，同时优化图片资源。再者就是使用 CSS Sprite 或者对于较小的图片用 base64 直接编码来进行优化。当然还有很多可以优化的方向，例如考虑浏览器缓存、页面渲染性能 ( 减少重排与重绘与 GPU 硬件加速 ) 、JS阻塞性能等等。但我们今天讲的是如何利用缓存策略在适宜的情况下直接减少对前端数据的请求量从而达到前端性能的优化。因此 Service Worker 以及其相关的 API 就成为了我们今天的主角。

[TypeScript 技巧拾遗](https://mp.weixin.qq.com/s/pMGOecpnploICBqoAAg9fQ)
很早以前就尝试过使用 TypeScript 来进行日常编码，但自己对静态类型语言的了解并不深入，再加上 TypeScript 的类型系统有着一定的复杂度，因此感觉自己并没有发挥好这门语言的优势，使代码变得更具可读性与可维护性。于是这几天便想着好好研究下这门语言，希望能够总结出一些特别的语言特性与实用技巧。

[从零开始写一个符合Promises/A+规范的promise](https://mp.weixin.qq.com/s/JMnJUCVLMX15mTkIe4U6lQ)
本篇主要讲解如何从零开始一步步的实现promise各项特性及功能，最终使其符合Promises/A+规范，因为讲解较细，所以文章略长。另外，每一步的项目源码都在github上，可以对照参考，每一步都有对应的项目代码及测试代码，喜欢的话，欢迎给个star~

[「手摸手设计模式系列」享元模式与资源池](https://mp.weixin.qq.com/s/sPVuiUTOvu1KsRwEOhMQ2g)
享元模式 （Flyweight Pattern）运用共享技术来有效地支持大量细粒度对象的复用，以减少创建的对象的数量。

[前端中的编译原理 - 从零打造一个实用的 Babel 插件](https://mp.weixin.qq.com/s/XSzQ5nkLI369CDhMZtF-MQ)
说起编译原理，可能我们脑海中首先浮现的就是 “编译器” 这个词汇。维基百科上对编译器的定义是：编译器是一种计算机程序，它会将某种编程语言写成的源代码（原始语言）转换成另一种编程语言（目标语言）。 通常一个编译器的编译过程会经过词法分析、语法分析、语义分析、生成中间代码、优化、生成目标代码这几个阶段。如果将其简要概括，则只包含 解析 ( parse ) 、转换 ( transform ) 、生成 ( generate ) 这三个阶段。

[前端工程师需要了解的 Babel 知识](https://mp.weixin.qq.com/s/zQ5kCgoUbTOTqARI5o2cBQ)
在前端圈子里，对于 Babel，大家肯定都比较熟悉了。如果哪天少了它，对于前端工程师来说肯定是个噩梦。Babel 的工作原理是怎样的可能了解的人就不太多了。
