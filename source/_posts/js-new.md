---
title: 模拟实现new运算符功能
date: 2020-05-18 09:51:45
tags:
  - JavaScript
---

new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一

示例：
```javascript
function Wechat() {
  this.name = 'fedaily'
}
Wechat.prototype.getName = function() {
  return this.name
}

const wechat = new Wechat()
// wechat.name === fedaily
// wechat.getName() === fedaily
```

我们可以看到，实例可以访问到构造函数的属性以及原型对象上的属性。

所以我们可以模拟一下：
```javascript
function newFactory() {
  var obj = new Object(),
  Constructor = [].shift.call(arguments);
  obj.__proto__ = Constructor.prototype;
  // 处理构造函数有返回值的情况
  // 返回值如果是一个object则直接返回，否则返回obj
  var ret = Constructor.apply(obj, arguments);
  return typeof ret === 'object' ? ret : obj;
};
```
