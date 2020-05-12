---
title: 如何实现一个LRU算法
date: 2020-05-11 10:46:46
tags:
  - 算法
---

LRU算法是指最近最少使用算法。

那么如何实现这种算法呢？我们以数组为例先来分析一下思路：

假设我们有一个数组，我们规定数组越靠前面的元素代表即将淘汰的元素，而数组最后面元素代表最近有使用过的数据。

当我们从缓存中访问一个数据的时候：
1. 如果该数据存在，则将该数据放到数组最后面，并返回该数据
2. 如果数据不存在，则返回-1

当我们往缓存中添加一个数据的时候：
1. 如果该数据存在，则删除对应的元素(其实本意是要将该数据移动到数组最后面，这里直接先删除，后面再添加)
2. 判断数组长度是否超过限制
3. 如果超过限制，则删除数组最前面的元素，即下标为0的元素
4. 将数据添加到数组最后面

**通俗点来说，就是只要这条数据被访问了或者是添加到缓存中，我们都将它放到数组最后面。**

**如果添加时候容量已经满了，就把数组最前面的元素删除。**

根据这种思路，我们就可以实现一个LRU的算法。可以使用链表、数组、Map等等去实现。

这里以一个数组为例：(这种方式其实效率很差，只为了理解思路，根据这个思路，你可以用链表等其他更高效的方式去实现)
```
class LRUCache {
  constructor(capacity) {
    this.pool = [];
    this.capacity = capacity;
  }

  hasKey(key) {
    return this.pool.findIndex(item => item.key === key);
  };

  put(key, value) {
    // 如果key存在，就删除这个元素，因为后面会直接添加到数组最后面
    const index = this.hasKey(key);
    if (index >= 0) {
      this.pool.splice(index, 1);
    }

    // 超过容量就把第一个扔掉
    if (this.pool.length >= this.capacity) {
      this.pool.shift();
    }
    this.pool.push({ key, value });
  }

  get(key) {
    // 访问了某个key，就需要把这个key对应的元素放到数组最后面
    const index = this.hasKey(key);
    const rtv = this.pool[index];
    if (index >= 0) {
      this.pool.splice(index, 1);
      this.pool.push(rtv);
      return rtv.value;
    }
    return -1;
  }
}
```
