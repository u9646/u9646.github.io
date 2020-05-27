---
title: 第27期 这就是你日思夜想的React原生动态加载 & GraphQL到底怎么使 & 一文从零彻底搞懂前端的内存监控、异常、泄漏
date: 2020-05-25 21:50:19
tags:
  - 精选
---

[这就是你日思夜想的 React 原生动态加载](https://mp.weixin.qq.com/s/l_kv6rzUXSF3R9bfIko5BQ)

在 React 16.6 版本中，新增了 React.lazy 函数，它能让你像渲染常规组件一样处理动态引入的组件，配合 webpack 的 Code Splitting，只有当组件被加载，对应的资源才会导入 ，从而达到懒加载的效果。

[GraphQL到底怎么使？看看智联前端团队技术沉淀](https://mp.weixin.qq.com/s/Zps62tAV5gTVqkWUU8msqA)

此文是作者考虑 GraphQL 在 Node.js 架构中的落地方案后所得。从最初考虑可以（以内置中间件）加入基础服务并提供完整的构建、发布、监控支持，到最终选择不改动基础服务以提供独立包适配，不限制实现技术选型，交由业务团队自由选择的轻量方式落地。中间经历了解除误解，对收益疑惑，对最初定位疑惑，最终完成利弊权衡的过程。

[Chrome 83 发布，支持直接读写本地文件！新的跨域策略!](https://mp.weixin.qq.com/s/6fDoKE2bayKXvI5re-gfOg)

受新冠疫情影响，Chrome 稳定版本的更新直接跳过 v82 来到 Chrome 83，因此很多原本在 Chrome 82上就要正式发布的功能也悉数积攒到了本次更新的 Chrome 83 中。

[一文从零彻底搞懂前端的内存监控、异常、泄漏](https://mp.weixin.qq.com/s/wufb9IE0ptpMMUso7SZetQ)

垃圾回收机制通常是使用标志清除策略，简单说，也就是引用从根节点开始是否可达来判定是否是垃圾

上面是发生内存泄漏的根本原因，直接原因则是，当不同生命周期的两个东西相互通信时，一方生命到期该回收了，却被另一方还持有时，也就发生内存泄漏了

所以，下面就来讲讲，哪些场景会造成内存泄漏

[前端组件库本地开发调试的自动化流程实现](https://mp.weixin.qq.com/s/nOFKKCwUDCYW1HAGH25W-Q)

如果不对项目进行设计，我们写的所有代码都会存于同一个仓库，当需要本地调试编码效果时，一条 npm run start 或者 npm run dev 便可把我们所需的服务启动，但随着时间推移，这个项目无法避免会成为一个「巨无霸」，而其带给我们的伤害会远大于收益，于是我们想到了拆分，那么，就将一些 UI 或者计算逻辑单独抽成包吧。

[React源码揭秘(三)：Diff算法详解](https://mp.weixin.qq.com/s/e0fDbM5pp9bX1YuYBSXhLA)

在上一篇文章这里3我们讲到，在render阶段的beginWork函数中，会将上次更新产生的 Fiber 节点与本次更新的 JSX 对象（对应ClassComponent的this.render方法返回值，或者FunctionComponent执行的返回值）进行比较。根据比较的结果生成workInProgress Fiber，即本次更新的 Fiber 节点。

**公众号: 前端收藏家。收集全网优秀前端技术资讯，与你分享，共同成长。**