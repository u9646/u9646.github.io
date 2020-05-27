---
title: 如何快速将你的SPA应用改造成PWA应用
date: 2020-05-26 21:05:56
tags:
  - pwa
---

PWA是指渐进式web应用，一般由web app manifest, service worker, app shell, notification push等部分组成。

这里目前只讨论web app manifest, service worker两部分如何应用到项目中。

web app manifest可以使应用变成可安装式的。可以通过浏览器将应用添加到桌面上，从而可以从桌面上直接访问，同时可以定义桌面图标、应用名称、系统状态栏颜色，通过这些，我们可以让我们的应用像一个原生APP一样。从而给用户更好的体验。

从产品的角度来看，用户从桌面图标访问我们的应用肯定比通过浏览器去访问便捷的更多，同时还可以让用户访问的时候隐藏浏览器本身的一些控件，比如导航栏、底部设置等控件。而实现这一切只需要很小的代价。这一切只需要添加一个manifest.json文件即可。

⚠️注意：在你开始跃跃欲试之前，请先确认你的应用是否全站https。如果不是，那么最好等你的应用全站https后再来尝试。

#### Web App Manifest

我们看下manifest.json文件是什么样的：

[Web App Manifest 文档](https://developer.mozilla.org/zh-CN/docs/Web/Manifest)

```json
{
  "name": "前端收藏家",
  "short_name": "fedaily",
  "orientation": "portrait",
  "display": "standalone",
  "start_url": "/",
  "description": "只收藏好的，只推荐好的",
  "background_color": "#000",
  "theme_color": "#000",
  "icons": [
    {
      "src": "icon.png",
      "sizes": "96x96",
      "type": "image/png"
    },
    // ... 可以添加多种尺寸的图标
  ]
}
```

针对iOS平台，我们需要使用标签的形式添加：

[iOS application 官方文档](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html)

```html
<link rel="apple-touch-icon" href="icon.png">
<meta name="apple-mobile-web-app-title" content="前端收藏家">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black">
<meta name="theme-color" content="#000000">
```

然后在你的html中引入这个json文件即可。
```html
<link rel="manifest" href="/manifest.json">
```

⚠️注意，这里需要放在项目根目录下，否则会导致安卓不能自动弹出添加到桌面的提示（你可能会问，那iOS会有这个问题吗，没有，iOS根本就不支持这个特性）。

当你做好这些，一个可以安装到桌面的应用就诞生了。

你可以用Android的chrome浏览器访问你的应用，然后通过设置-添加到桌面功能，就可以将你的应用添加到桌面了。
你可以用iOS的safari浏览器访问你的应用，然后通过底部的分享按钮-添加到主屏幕，就可以将你的应用添加到桌面了。

#### Service Worker

Service worker是一个注册在指定源和路径下的事件驱动worker。它采用JavaScript控制关联的页面或者网站，拦截并修改访问和资源请求，细粒度地缓存资源。你可以完全控制应用在特定情形（最常见的情形是网络不可用）下的表现。

Service worker运行在worker上下文，因此它不能访问DOM。相对于驱动应用的主JavaScript线程，它运行在其他线程中，所以不会造成阻塞。它设计为完全异步，同步API（如XHR和localStorage）不能在service worker中使用。

出于安全考量，Service workers只能由HTTPS承载，毕竟修改网络请求的能力暴露给中间人攻击会非常危险。在Firefox浏览器的用户隐私模式，Service Worker不可用。

service worker具体的API以及详细介绍，大家可以在网上搜相关的资料，都说的很详细，这里介绍一个非常便捷的方法。

[workbox webpack plugin](https://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin)可以和webpack集成，只需要很少的配置，就可以实现大部分我们需要的功能。

我们看下如何使用：
```js
new GenerateSW({
  swDest: 'sw.js',
  skipWaiting: true,
  clientsClaim: true,
  cleanupOutdatedCaches: true,
  inlineWorkboxRuntime: true,
  runtimeCaching: [{
    handler: 'NetworkFirst',
    urlPattern: /\/api/,
    method: 'GET',
  }]
})
```

这个插件会读取webpack本身构建出来的静态资源文件，比如.js, .css, .png等资源，将其添加到缓存列表中，所以不需要在这里再指定了。

通过runtimeCaching，我们可以缓存比如api的返回值，这里⚠️建议慎重考虑，如果你不是特别在意应用在断网情况下的体验（作为一个web应用，大多数情况下，断网基本啥都干不了，所以尽量不要缓存api数据，缓存静态资源即可，缓存策略不合适的话，可能会导致一些意想不到的BUG🐛）

这个插件会根据你的配置自动生成service worker文件，是不是很简单，然后你需要做的就是去你的html里面注册servie worker就可以了。
```html
<script>
  if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
      navigator.serviceWorker.register(`/sw.js?${Date.now()}`).then(registration => {
        console.log('SW registered: ', registration)
      }).catch(registrationError => {
        console.log('SW registration failed: ', registrationError)
      })
    })
  }
</script>
```

⚠️注意：sw.js文件和manifest.json一样，也需要放在项目根目录下的。

到这里，其实我们就有一个基本的可以使用的pwa化的应用了。

如果你还需要一些定制化的需求可以详细阅读workbox文档去实现。

### 开发中遇到的问题

#### 安卓的chrome没有弹添加到桌面的弹窗
检查你的manifest.json, sw.js文件是否在项目根目录下。以及sw是否注册成功。

#### 安卓添加到桌面过程很慢
暂时还没有找到好的解决方案

#### 如何判断是否从主屏幕访问
因为埋点等需求，我们需要区分用户是从浏览器访问的还是从主屏幕访问的，可以这样判断：
```
export const isFromDesktop = () => {
  // window.navigator.standalone used for safari
  if (window.navigator.standalone) {
    return true;
  }
  if (window.matchMedia && window.matchMedia('(display-mode: standalone)').matches) {
    return true;
  }
  return false;
};
```

#### 如何监听添加到桌面事件
```js
let deferredPrompt
window.addEventListener('beforeinstallprompt', event => {
  console.log('install event trigger', event);
  // 禁止浏览器自动显示添加到桌面提示框
  e.preventDefault();
  // 存储事件对象，方便后面自定义弹窗时机
  deferredPrompt = e;

  // 然后可以显示一个自定义的提示框给用户，告诉用户
  // 可以将应用添加到桌面，同时可以告知如何操作
})

// 再监听自定义提示框的按钮点击事件
buttonInstall.addEventListener('click', (e) => {
  // 隐藏我们自定义的弹窗
  hideMyInstallPromotion();
  // 显示添加到桌面提示框（浏览器提供的）
  deferredPrompt.prompt();
  // 这里可以获取到用户的选择，同时做一些埋点相关操作
  deferredPrompt.userChoice.then((choiceResult) => {
    if (choiceResult.outcome === 'accepted') {
      console.log('用户接受');
    } else {
      console.log('用户拒绝');
    }
  })
});
```

#### 添加到主屏幕失败
安卓浏览器添加到主屏幕权限可能没打开，如果没打开，也不会有添加到主屏幕那个提示框的，需要开启权限再次尝试（所以一个自定义的提示框还是很重要的，可以告知用户如何处理失败的情况，经过测试，因为权限导致的失败并没有什么提示给到用户）

#### 如何捕捉安装成功动作
```js
window.addEventListener('appinstalled', (evt) => {
  console.log('app installed', evt);
})
```

#### 如何注销sw
```js
// registrations 是注册成功回掉的参数
for (let registration of registrations) {
  registration.unregister()
}
```

#### 如何降级
建议配置一个接口，根据接口决定是否执行sw注册的流程，否则将已有的sw注销掉。这样可以很方便的处理线上问题。比如：
```js
fetch('/api/sw/').then(result => {
  if (result) {
    // 注册操作
  } else {
    // 注销操作
  }
})
```

写到这里，好像实际开发过程中问题还是挺多的，不过综合来看，PWA还是非常值得尝试的。毕竟体验大不同呀。

后面遇到其他问题再更新，也欢迎大家积极留言交流。。。

**公众号: 前端收藏家。收集全网优秀前端技术资讯，与你分享，共同成长。**