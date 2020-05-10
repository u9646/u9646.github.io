---
title: 如何实现一个优雅的jsBridge
date: 2020-05-08 17:17:45
tags:
  - JavaScript
---

### 什么是jsbridge
jsbridge是客户端和H5沟通的桥梁，通过它，我们可以获取部分原生能力，同时客户端也可以使用我们提供的一些方法。实现双向通信。

### jsbridge原理
客户端可以通过webview里面注入一些javascript的上下文，可以理解为在window对象上挂载了一些方法，然后H5通过特定的对象可以获取到这个方法，反过来也是一样，js挂载了一些方法到window对象上，客户端也就可以调用js的某些方法。

### 具体实现

#### 方案一：注入API

##### IOS UIWebView

```
JSContext *context = [uiWebView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];

context[@"postBridgeMessage"] = ^(NSArray<NSArray *> *calls) {
    // Native 逻辑
};
```

H5使用`window.postBridgeMessage(message)`调用

##### IOS WKWebView
```
@interface WKWebVIewVC ()<WKScriptMessageHandler>

@implementation WKWebVIewVC

- (void)viewDidLoad {
    [super viewDidLoad];

    WKWebViewConfiguration* configuration = [[WKWebViewConfiguration alloc] init];
    configuration.userContentController = [[WKUserContentController alloc] init];
    WKUserContentController *userCC = configuration.userContentController;
    // 注入对象，前端调用其方法时，Native 可以捕获到
    [userCC addScriptMessageHandler:self name:@"nativeBridge"];

    WKWebView wkWebView = [[WKWebView alloc] initWithFrame:self.view.frame configuration:configuration];

    // TODO 显示 WebView
}

- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
    if ([message.name isEqualToString:@"nativeBridge"]) {
        NSLog(@"前端传递的数据 %@: ",message.body);
        // Native 逻辑
    }
}
```

H5使用`window.webkit.messageHandlers.nativeBridge.postMessage(message)`方式调用。

来看一下H5端具体实现

假设我们需要一个`getUserInfo`方法，用于H5获取当前APP登录用户的信息。

那么我们js可以这样：

```
import registerCallback from '../registerCallback';

export default function getUserInfo() {
  return new Promise((resolve, reject) => {
    try {
      window.webkit.messageHandlers.getUserInfo.postMessage({
        callback: registerCallback(resolve),
      });
    } catch (e) {
      reject(e);
    }
  });
}
```

我们定义一个`getUserInfo`方法，这个方法会去调用`window.webkit.messageHandlers.getUserInfo.postMessage`方法，这是客户端写入的方法，然后传入一个callback方法。客户端会通过callback方法把我们需要的信息返回给我们。

我们可以再看下`registerCallback`这个方法是什么：
```
window.knCallbacks = {};

function makeRandomId(func) {
  return `${func.name || 'anonymous'}_${Date.now()}`;
}

export default function registerCallback(callback, keepAlive) {
  if (!callback) {
    return null;
  }

  const callbackId = makeRandomId(callback);
  window.knCallbacks[callbackId] = (data) => {
    let result;
    if (typeof data === 'object') {
      result = data;
    } else if (typeof data === 'string') {
      try {
        result = JSON.parse(data);
      } catch (e) {
        result = data;
      }
    }
    callback(result);
    if (!keepAlive) {
      delete window.knCallbacks[callbackId];
    }
  };

  return `knCallbacks.${callbackId}`;
}
```

在这个方法里面，我们会为promise的resolve方法生成一个随机函数名，避免出现window可能会有同名函数导致将其覆盖的问题。
然后我们根据生成的函数名重写一个函数，将这个函数最后返回给外部的callback，传递给了客户端。
这里的data就是客户端调用callback传递过来的数据。
我们在这里对数据做一些处理，然后给到callback，也就是promise的resolve。

##### Android

客户端实现：
```
publicclassJavaScriptInterfaceDemoActivityextendsActivity{
  private WebView Wv;

  @Override
  publicvoidonCreate(Bundle savedInstanceState){
    super.onCreate(savedInstanceState);
    Wv = (WebView)findViewById(R.id.webView);
    final JavaScriptInterface myJavaScriptInterface = new JavaScriptInterface(this);
    Wv.getSettings().setJavaScriptEnabled(true);
    Wv.addJavascriptInterface(myJavaScriptInterface, "knJSBridge");
    // TODO 显示 WebView
  }

  publicclassJavaScriptInterface{
    Context mContext;
    JavaScriptInterface(Context c) {
        mContext = c;
    }
    publicvoidpostMessage(String webMessage){
        // Native 逻辑
    }
  }
}
```

H5实现
```
import registerCallback from '../registerCallback';

export default function getUserInfo() {
  return new Promise((resolve, reject) => {
    try {
      window.knJSBridge.getUserInfo(JSON.stringify({
        callback: registerCallback(resolve),
      }));
    } catch (e) {
      reject(e);
    }
  });
}
```

这里我们其实可以看到，knJSBridge其实是我们和客户端约定的一个字段。然后我们通过`window.knJSBridge`就可以调用客户端的方法了。
registerCallback和IOS的是一样的。

根据上面我们H5这里最后可以实现这样一个结构的jsbridge

```
jsbridge
  - index.js
  - ios/
    - index.js
    - getUserInfo.js
  - android/
    - index.js
    - getUserInfo.js
```

```
// jsbridge/index.js
import iosBridge from './iOs';
import androidBridge from './android';

export default class Bridge {
  constructor() {
    super();
    if (isAndroid) {
      this.jsbridge = androidBridge;
    } else {
      this.jsbridge = iosBridge;
    }
  }
  getUserInfo = (...args) => this.jsbridge.getUserInfo(...args);
}

// ios/index.js
import getUserInfo from './getUserInfo';

export {
  getUserInfo
}

// ios/getUserInfo.js
import registerCallback from '../registerCallback';

export default function getUserInfo() {
  return new Promise((resolve, reject) => {
    try {
      window.webkit.messageHandlers.getUserInfo.postMessage({
        callback: registerCallback(resolve),
      });
    } catch (e) {
      reject(e);
    }
  });
}

// android/index.js
import getUserInfo from './getUserInfo';

export {
  getUserInfo
}

// android/getUserInfo.js
import registerCallback from '../registerCallback';

export default function getUserInfo() {
  return new Promise((resolve, reject) => {
    try {
      window.webkit.messageHandlers.getUserInfo.postMessage({
        callback: registerCallback(resolve),
      });
    } catch (e) {
      reject(e);
    }
  });
}
```

#### 方案二：url拦截
这种方案就是H5构造一个iframe，通过给iframe设置src发起请求，然后客户端拦截请求实现。

一般会将url设为一个特殊的字符串，比如`https://__bridge__`这个样子，然后后面跟上我们需要传递的数据，包括一个callback函数名，客户拦截这种请求，然后通过callback将数据传递回来。
