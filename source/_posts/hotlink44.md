---
title: 第44期 分析React组件的渲染性能 & Node Buffers完整指南
date: 2020-06-17 20:43:52
tags:
  - 精选
---

[了不起的 Webpack HMR 学习指南（含源码分析）](https://mp.weixin.qq.com/s/Nd1bXGA5uacFN_guP0Gx3Q)

Hot Module Replacement（以下简称：HMR 模块热替换）是 Webpack 提供的一个非常有用的功能，它允许在 JavaScript 运行时更新各种模块，而无需完全刷新。

[运用Taro - 快捷开发企业业务之小易小程序](https://mp.weixin.qq.com/s/d5KzQfy1CQFSk0L2p51z2Q)

Taro 是由 JDC·凹凸实验室 倾力打造的一款多端开发解决方案的框架工具，它遵循 React 语法规范，采用与 React 一致的组件化思想。使用 Taro 进行开发可以获得和 React 一致的开发体验，它的组件生命周期和 React 完全保持一致，同时也支持 JSX 语法。通过使用 Taro 框架工具开发，仅需要写一套代码就可以利用 Taro 的编译工具，将源代码分别编译出可以在不同端（微信 / 京东 / 百度 / 支付宝 / 字节跳动 小程序、快应用、H5、React-Native 等）运行的代码。

[分析 React 组件的渲染性能](https://mp.weixin.qq.com/s/tDWBFlZf-rh_IvlSqKTcgg)

今天，我们介绍一下如何使用 React Profiler API 分析 React 组件的渲染性能。

[Node Buffers 完整指南](https://mp.weixin.qq.com/s/Z7S4hCAv6ObYayqmATYfog)

身为 Node 开发人员这些年来，你是否从未遇到过 Node 缓冲区（Buffer）这个概念呢？也许这个术语你见过几次，但每次都不想一探究竟？你可能的确没遇到过要使用缓冲区的场景，毕竟 Node.js 并不是那种要求程序员直接和程序管理内存的操作打交道的语言。但是，如果你想要成为专业 Node 开发人员，愿意为此付出更多的努力，那么你就必须深入探索缓冲区之类的概念，从而理解 Node 的底层工作机制。

[ESLint 在中大型团队的应用实践](https://mp.weixin.qq.com/s/4uzh0-mvAjxQwuWVB2hQUQ)

代码规范是软件开发领域经久不衰的话题，几乎所有工程师在开发过程中都会遇到，并或多或少会思考过这一问题。随着前端应用的大型化和复杂化，越来越多的前端工程师和团队开始重视 JavaScript 代码规范。得益于前端开源社区的繁盛，当下已经有几种较为成熟的 JavaScript 代码规范检查工具，包括 JSLint、JSHint、ESLint、FECS 等等。本文主要介绍目前较为通用的方案——ESLint，它是一款插件化的 JavaScript 代码静态检查工具，其核心是通过对代码解析得到的 AST（Abstract Syntax Tree，抽象语法树）进行模式匹配，定位不符合约定规范的代码。