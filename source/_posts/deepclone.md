---
title: js 如何实现深拷贝deepClone
date: 2020-05-31 17:08:57
tags:
  - JavaScript
---

如果不考虑RegExp, Function, Symbol, 循环引用的问题，那么使用JSON是一个不错的选择。

比如有这样一个对象：
```js
var target = {
  name: 'fedaily',
  age: 1,
  topics: ['react', 'vue', 'css']
}

var copyTarget = JSON.parse(JSON.stringify(target))
console.log(copyTarget) // { name: 'fedaily', age: 1, topics: ['react', 'vue', 'css'] }

copyTarget.topics.push('webpack')

// target不变
console.log(target) // { name: 'fedaily', age: 1, topics: ['react', 'vue', 'css'] }
```

但是如果需要考虑各种引用类型，以及循环引用等问题，那么还是需要手动实现这些拷贝逻辑，这里参照lodash深拷贝的实现逻辑，但是精简了很多，我们来看看如何做：
```js
const regexpTag = '[object RegExp]'

function deepClone(value, stack = new WeakMap()) {
  if (!isObject(value)) {
    return value
  }

  let result = Array.isArray(value) ? [] : {}

  // 函数直接返回
  if (typeof value === 'function') {
    return value
  }

  // 处理引用类型的拷贝
  result = initCloneByTag(value, getTag(value))

  // 处理循环引用
  if (stack.has(value)) {
    return stack.get(value)
  }
  stack.set(value, result)

  // 这里没有处理key是Symbol的情况
  // for in 不会枚举Symbol的key
  // 可以通过Object.getOwnPropertySymbols获取所有Symbol的key
  for (let key in value) {
    result[key] = deepClone(value[key], stack)
  }
  return result
}

function isObject (value) {
  const type = typeof value
  return value != null && (type === 'object' || type === 'function')
}

function getTag(value) {
  if (value == null) {
    return value === undefined ? '[object Undefined]' : '[object Null]'
  }
  return Object.prototype.toString.call(value)
}

function cloneRegExp(regexp) {
  const result = new regexp.constructor(regexp.source, /\w*$/.exec(regexp))
  result.lastIndex = regexp.lastIndex
  return result
}

function initCloneByTag(object, tag) {
  const Ctor = object.constructor
  // 可以在这里处理
  // arrayBuffer, int32array, dataview等情况
  switch (tag) {
    case regexpTag:
      return cloneRegExp(object)

    default:
      return {}
  }
}
```