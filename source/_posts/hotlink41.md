---
title: 第41期 TypeScript 期中考试现在开始 & 一起来玩玩WebGL & MVC的不足与Flux的崛起
date: 2020-06-14 17:43:47
tags:
  - 精选
---

[MVC的不足与Flux的崛起](https://mp.weixin.qq.com/s/BB1VUmtQ5uBJ7VSnRf11ZQ)

在前几篇中，我演示了一个前端 Backbone.js MVC 框架用于解决实际问题的例子。但 MVC 依然存在几个问题:

1. 不可预测：当一个事件发生之后，你并不知道会有谁响应这个事件，是单个对象还是多个对象会响应这个事件
2. 级联修改：当一个事件发生之后，A 组件在接收到事件之后在响应的过程中，还可能发出其他的事件触发后续的修改，你并不知道这个事件会在何处结束，会造成什么样的结果。这也和上一条「不可预测」相对应
3. 响应顺序：如果存在多个对象响应同一个事件的话，有时候对响应的顺序是有要求的，某些变更不可以出现在其他的变更之前
4. 有条件响应：对于传播方而言，并非希望所有的时间都一视同仁的广播出去；对于消费方而言，也并不希望一视同仁的响应所有的事件

[一起来玩玩WebGL](https://mp.weixin.qq.com/s/bR5IBdETINDcVsqMx6Xl_w)

上一篇文章说到我从客户端转前端的历程，短短一年的时间就打开了前端世界的大门，简直就是有无穷多的东西可玩，以前酷爱Java的我终于见识到什么都可以写的JavaScript的厉害了，不仅仅可以写Web，客户端，后端，系统应用，还可以在神经网络、物联网，甚至嵌入式都可以，简直就是一个万能的语言，可以说能编程的地方理论上都可以用JS来写！

[使用JS和NodeJS爬取Web内容](https://mp.weixin.qq.com/s/7TQL3VLdDxSXn-m1ShWs1Q)

这些年来 Javascript 进步飞快，又引入了称为 NodeJS 的运行时，所以已经成为了最流行和使用最广泛的语言之一。不管你要写的是 Web 应用还是移动应用，都能在 Javascript 生态中找到合适的工具。本文要介绍的是如何在 NodeJS 的活跃生态系统帮助下高效地抓取 Web 内容，以满足大多数相关需求。

[TypeScript 期中考试现在开始](https://mp.weixin.qq.com/s/fWUQcbzryoZuCnF2lVeuzA)

本文从最近在 Github 上比较火的仓库 typescript-exercises[2] 入手，它的中文介绍是 「富有挑战性的 TypeScript 练习集」。里面包含了 15 个 TypeScript 的练习题，我会从其中挑选出几个比较有价值的题目，一起来解答一下。

[一文读懂 TypeScript 泛型及应用](https://mp.weixin.qq.com/s/cTeUyDoE6n8DS3RRRvy6rw)

觉得 TypeScript 泛型有点难，想系统学习 TypeScript 泛型相关知识的小伙伴们看过来，本文从八个方面入手，全方位带你一步步学习 TypeScript 中泛型，详细的内容大纲请看下图：