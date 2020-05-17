---
title: interface 和 type aliases 区别
date: 2020-05-17 16:45:51
tags:
  - TypeScript
---

[TypeScript interface vs type aliases](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-aliases)

在大多数情况下，interface和type别名并没有太大的区别。但是，它们有一些微妙的区别。

其中一个区别就是interface会创建一个新的类型名称，并且可以在任何地方使用。但是type没有，它只是一个引用，并没有创建一个新的类型实例。

看下面这个例子，当我们鼠标hover到interfaced的时候，编辑器会提示当前函数返回一个Interface。而当我们hover到aliased的时候，返回的是一个{ num: number }对象。
```javascript
type Alias = { num: number };
interface Interface {
  num: number;
}
declare function aliased(arg: Alias): Alias;
declare function interfaced(arg: Interface): Interface;
```

在老版本的typescript中，类型别名是不支持extended或者implemented（也不支持从其他类型别名extend/implement）

在2.7版本后，类型别名可以通过交叉类型实现extend。例如`type Cat = Animal & { purrs: true }`

根据软件的[开闭原则](https://en.wikipedia.org/wiki/Open/closed_principle), 你应该尽可能的使用interface代替type alias。

从另一方面说，如果你不能使用interface表达你的类型，需要使用到联合类型或者元组，那么就可以使用type alias。

### 英文原文：
As we mentioned, type aliases can act sort of like interfaces; however, there are some subtle differences.

One difference is that interfaces create a new name that is used everywhere. Type aliases don’t create a new name — for instance, error messages won’t use the alias name. In the code below, hovering over interfaced in an editor will show that it returns an Interface, but will show that aliased returns object literal type.
```javascript
type Alias = { num: number };
interface Interface {
  num: number;
}
declare function aliased(arg: Alias): Alias;
declare function interfaced(arg: Interface): Interface;
```

In older versions of TypeScript, type aliases couldn’t be extended or implemented from (nor could they extend/implement other types). As of version 2.7, type aliases can be extended by creating a new intersection type e.g. type Cat = Animal & { purrs: true }.

Because [an ideal property of software is being open to extension](https://en.wikipedia.org/wiki/Open/closed_principle), you should always use an interface over a type alias if possible.

On the other hand, if you can’t express some shape with an interface and you need to use a union or tuple type, type aliases are usually the way to go.
