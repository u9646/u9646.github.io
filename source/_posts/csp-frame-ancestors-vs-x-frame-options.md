---
title: CSP frame-ancestors 对比 X-Frame-Options
date: 2020-03-28 10:01:46
tags:
  - 安全
---

> 这两个字段都是用于控制别的网站使用iframe或者frame加载你的网站的行为

### X-Frame-Options
该字段有三个可能的值，分别为`deny`,  `sameorigin`, `allow-from https://example.com/`；

#### deny
`deny`表示禁止任何使用iframe或者frame加载你的网站。

#### sameorigin
`sameorigin`表示只允许同源的网站使用iframe或者frame加载你的网站

#### allow-from
`allow-from`表示只允许该网站使用iframe或者frame加载你的网站。

注意：强烈建议不要使用该属性，有两个问题：

1. 浏览器兼容性很差，具体可参考 [MDN X-Frame-Options](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/X-Frame-Options)
2. 只能填写一个白名单地址

以chrome v80 为例：
![](https://github.com/u9646/u9646.github.io/blob/master/csp-frame-ancestors-vs-x-frame-options/allow-from-test.png?raw=true)
虽然allow-from写成了3100端口，但是页面依然加载出来了。经过测试，safari, firefox（和下面版本相同）都可以正常加载，说明它们都不支持该属性。

#### 如何使用？

> ***设置 meta 标签是无效的！例如 `<meta http-equiv="X-Frame-Options" content="deny">` 没有任何效果。不要这样用！只有当像下面示例那样设置 HTTP 头 X-Frame-Options 才会生效。***

在请求头中增加`X-Frame-Options`例如：`X-Frame-Options: sameorigin`

### CSP frame-ancestors
CSP是指[Content-Security-Policy](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Security-Policy)。

`frame-ancestors`是其中一个指令。指定了一个可以包含`<frame>`，`<iframe>`，`<object>`，`<embed>`，or `<applet>`等元素的有效父级。

> ***该指令不支持通过<meta> 元素或通过 `Content-Security-policy-Report-Only` 头域所指定.***

当`frame-ancestors`设为`none`时，作用类似于`X-Frame-Options: deny`。

`frame-ancestors`策略可以设置一个或多个源`<source>`：

#### `<host-source>`
主机的名称或IP地址，以及一个可选的URL scheme和／或端口号

例如：
* http://*.example.com: 匹配所有使用http:URL scheme并来对于example.com及其子域名的加载意图。
* mail.example.com:443: 匹配所有对于mail.example.com在443端口的访问意图。
* https://store.example.com: 匹配所有使用https:访问store.example.com的意图。

#### `<scheme-source>`
一个schema配置，比如'http:'或'https:'。注意，冒号是必要的。你同样也可以指定一个data schema（但并不推荐）。

例如：
* 'data:' 允许 data: URIs 作为内容源。 这是不安全的，攻击者可以用此来注入恶意代码。请谨慎使用，并不要令其作用于脚本。
* 'mediastream:' 允许 mediastream: URIs 作为内容源.
* 'blob:' 允许 blob: URIs 作为内容源.
* 'filesystem:' 允许 filesystem: URIs 作为内容源.

#### self
指向一个该受保护文档所在的源，包含同样的URL schema和端口号。必须用单引号设置。有些浏览器会从源指令中排除blob和filesystem。需要允许这些内容类型的站点可以通过Data属性指定它们。

例如：`Content-Security-Policy: frame-ancestors 'self'`

#### none
指向一个空集，意味着没有URL会被匹配。也需要单引号包裹设置。
例如：`Content-Security-Policy: frame-ancestors 'none'`

可以同时配置多个规则：
`Content-Security-Policy: frame-ancestors 'self' https://example.com`


### 结论
当你只需要配置不允许别人使用iframe加载你的网站或者只允许同源网站的时候，使用X-Frame-Options即可。如果你需要使用白名单的方式控制其行为，那么你应该使用`Content-Security-Policy: frame-ancestors`，它支持配置多个白名单而且具有更好的浏览器兼容性。

当他们同时设置的时候，按规范`Content-Security-Policy: frame-ancestors`具有更高的优先级。但是现实往往不是如此，很多浏览器对此行为比不一致。建议只设置一个配置。

#### 测试：
> 这里以mac上面的几个常用浏览器为例，需要的小伙伴可以使用express自己在本地进行测试。

比如请求头同时有以下两个配置:
`Content-Security-Policy: frame-ancestors 'self' https://example.com`
`X-Frame-Options: sameorigin`
##### chrome v80
![](https://github.com/u9646/u9646.github.io/blob/master/csp-frame-ancestors-vs-x-frame-options/both-csp-xframe.png?raw=true)
可以看到chrome是可以正常加载的

##### safari v12
![](https://github.com/u9646/u9646.github.io/blob/master/csp-frame-ancestors-vs-x-frame-options/both-csp-xframe-safari.png?raw=true)
可以看到safari先判断了x-frame-options，然后就阻止了页面加载。

##### firefox v73
![](https://github.com/u9646/u9646.github.io/blob/master/csp-frame-ancestors-vs-x-frame-options/both-csp-xframe-firefox.png?raw=true)
可以看到火狐也是可以正常加载了
