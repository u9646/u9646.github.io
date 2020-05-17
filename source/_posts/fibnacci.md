---
title: 斐波那契数列
date: 2020-05-17 11:43:31
tags:
  - 算法
---

使用数组结构实现一个非递归的斐波那契数列

```javascript
const fibnacci = (n) => {
  if (n === 0) {
    return 0;
  }
  let a = 0;
  let b = 1;
  for (let i = 1; i <= n; i += 1) {
    [a, b] = [a, a + b];
  }
  return b;
}
```
