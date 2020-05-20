---
title: 第22期 Recoil-Facebook官方React状态管理器 & Facebook前端技术栈重构分享 & 我在阿里是怎么工作的，写在阿里入职一周年
date: 2020-05-18 20:36:56
tags:
  - 精选
---

[Recoil-Facebook官方React状态管理器](https://mp.weixin.qq.com/s/P6dBlR5OTq1Cx2RHtEuHzQ)

说到状态管理器，轮子满天飞。在 Class 时代，redux 与 mobx 几乎占据了全部市场，几乎没有没用过 redux 的同学。随着 Hooks 的诞生，新的一批轮子应运而生，其中有代表性的有 unstated-next、constate 等等。

最近，facebook 官方出了一个状态管理器解决方案 Recoil[1]，我们来体验一下。

[蚂蚁金服是如何提高移动端体验的？](https://mp.weixin.qq.com/s/2-j7ykMNn4IdYdjlcKDEHQ)

体验是一个很庞大的话题，有很多方面会影响产品的体验，如性能、UI、交互以及人性化的功能等等，本文抛砖引玉，只从技术层面的某几个方面聊聊移动端的体验优化，主要以 Android 为切入点，IOS 大部分优化方向与 Android 类似。考虑到市面上绝大多数 APP 都是 Native+H5 相结合的应用，且本人项目中也大量使用 H5 页面，因此将从 Native 端和 H5 端分别总结如何优化体验。

[我在阿里是怎么工作的，写在阿里入职一周年](https://mp.weixin.qq.com/s/EJKIxxPyy-_-YeV7b239xQ)

2019年初，笔者从腾讯跳槽到阿里。新的公司新的氛围，坦白说，阿里新岗位的工作强度大于之前在腾讯的岗位。我入职后不久后就迫于试用期答辩项目的压力，开始周末加班，后来又因为自己懒散，中断了坚持了一年多的博客更新。经过了一年多的阿里熏陶，我优化调整了自己部分的工作方法论并取得了不错的效果——重新找回了有节奏的工作生活。在这个过程中，总结了5条高效工作的经验，在这里分享给大家，希望能对大家有所帮助。

[Facebook前端技术栈重构分享](https://mp.weixin.qq.com/s/QyfcokJAdZr6fYR1L4Xm6g)

当我们考虑如何构建一个新的网络应用—一个为现代浏览器设计的、具有用户对Facebook（我们已知的）所有期望的功能，我们现有的技术栈无法支持我们所需要的类似于桌面应用的感觉和性能。完全重写是非常罕见的，但在这种情况下，由于过去十年来Web技术发生了很多变化，我们知道这是我们实现性能和未来可持续发展目标的唯一途径。今天，我们就分享一下我们在重构Facebook.com时的经验教训，使用React（一种用于构建用户界面的声明式JavaScript库）和Relay（React的GraphQL客户端）来重构Facebook.com。

[揭秘webpack插件工作流程和原理](https://mp.weixin.qq.com/s/LI-SkBoPA94Ply6Qes92PA)

通过插件我们可以扩展webpack，在合适的时机通过Webpack提供的 API 改变输出结果，使webpack可以执行更广泛的任务，拥有更强的构建能力。 本文将尝试探索 webpack 插件的工作流程，进而去揭秘它的工作原理。同时需要你对webpack底层和构建流程的一些东西有一定的了解。

[深度精读:浏览器渲染原理](https://mp.weixin.qq.com/s/qPxDYVpzmazZaSIcaVWMpQ)

本文从浏览器角度来告诉你，URL后输入后按回车，浏览器内部究竟发生了什么，读完本文后，你将了解到：

* 浏览器内有哪些进程，这些进程都有些什么作用
* 浏览器地址输入URL后，内部的进程、线程都做了哪些事
* 我们与浏览器交互时，内部进程是怎么处理这些交互事件的

### 前端收藏家（微信号: fedaily）
##### 收集全网优秀前端技术资讯，与你分享，共同成长。
