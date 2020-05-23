---
title: Object.assign的用法和注意点
date: 2020-05-23 10:46:02
tags:
  - JavaScript
---

Object.assign可以实现对象的合并。它的语法是这样的：
`Object.assign(target, ...sources)`

`Object.assign`会将source里面的**可枚举属性**复制到`target`。如果和target的已有属性重名，则会覆盖。同时后续的source会覆盖前面的source的同名属性。

⚠️Object.assign复制的是属性值，如果属性值是一个引用类型，那么复制的其实是引用地址，就会存在引用共享的问题。

举例说明：
```
const o1 = { name: 'fedaily' }
const o2 = { topic: ['react', 'nodejs'] }
const o3 = Object.assign({}, o1, o2)

// o3 { name: 'fedaily', topic: ['react', 'nodejs'] }
o3.topic.push('css')

// o3 { name: 'fedaily', topic: ['react', 'nodejs', 'css'] }
// o2 { topic: ['react', 'nodejs', 'css'] }
// ⚠️我们可以看到所有对象的topic值都被更新了
```

所以，当我们快乐的想要使用Object.assign的时候，需要特殊关注一下是否有引用类型哦。

如果我们需要处理引用类型怎么办呢，这就涉及到对象的拷贝了，可以参考lodash的_clone, _cloneDeep等方法。

再附上MDN关于Object.assign的polyfill，方便大家理解内部实现，同时更好的掌握这个方法哟
```
if (typeof Object.assign != 'function') {
  Object.assign = function (target, varArgs) { // .length of function is 2
    'use strict';
    if (target == null) { // TypeError if undefined or null
      throw new TypeError('Cannot convert undefined or null to object');
    }

    var to = Object(target);

    for (var index = 1; index < arguments.length; index++) {
      var nextSource = arguments[index];

      if (nextSource != null) { // Skip over if undefined or null
        for (var nextKey in nextSource) {
          // Avoid bugs when hasOwnProperty is shadowed
          if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
            to[nextKey] = nextSource[nextKey];
          }
        }
      }
    }
    return to;
  };
}
```

### 前端收藏家（微信号: fedaily）收集全网优秀前端技术资讯，与你分享，共同成长。