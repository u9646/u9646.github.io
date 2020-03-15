---
title: mongodb 增删改查CURD基本操作总结
date: 2020-03-04 20:49:35
tags:
  - mongodb
---

> 基于[mongodb v4.2](https://docs.mongodb.com/manual/introduction/)

## 插入数据

### 插入一条数据
`db.collection.insertOne()` 插入一个文档到集合中

```
try {
   db.products.insertOne( { item: "card", qty: 15 } );
} catch (e) {
   print (e);
};
// 返回值：
{
   "acknowledged" : true,
   "insertedId" : ObjectId("56fc40f9d735c28df206d078")
}
```

### 插入多条数据
`db.collection.insertMany()` 同时插入多条数据到集合中

```
try {
   db.products.insertMany( [
      { item: "card", qty: 15 },
      { item: "envelope", qty: 20 },
      { item: "stamps" , qty: 30 }
   ] );
} catch (e) {
   print (e);
}
// 返回值：
{
   "acknowledged" : true,
   "insertedIds" : [
      ObjectId("562a94d381cb9f1cd6eb0e1a"),
      ObjectId("562a94d381cb9f1cd6eb0e1b"),
      ObjectId("562a94d381cb9f1cd6eb0e1c")
   ]
}
```

insert 特性：
如果当前集合不存在，会自动创建一个集合。

_id 属性：
mongodb里面，如果你不提供_id，mongodb会默认在插入文档的时候为每一个文档创建一个唯一的_id，作为主键


## 查找数据
mongodb可以使用`find`方法查找数据

### 普通查询

> 每个文档插入到集合中如果不提供一个_id字段，则mongodb会自动生成一个，并且在查询语句中默认返回，这里省略没有显示该字段

```
// 示例数据
db.inventory.insertMany( [
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);
```

```
// 查找 size 等于 { h: 14, w: 21, uom: "cm" } 的数据
db.inventory.find( { size: { h: 14, w: 21, uom: "cm" } } )
// or 查找 qty 等于 25 的数据
db.inventory.find({ qty: 25 })
// or 使用嵌套查询 size 的 uom 等于 in 的数据
db.inventory.find( { "size.uom": "in" } )
// $lt 小于
db.inventory.find( { "size.h": { $lt: 15 } } )
// 复合条件查询，查询 size.h 小于 15， 并且 size.uom 等于 in，并且 status 等于 D 的数据
db.inventory.find( { "size.h": { $lt: 15 }, "size.uom": "in", status: "D" } )
```

### 查询数组
```
// 示例数据
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], dim_cm: [ 14, 21 ] },
   { item: "notebook", qty: 50, tags: ["red", "blank"], dim_cm: [ 14, 21 ] },
   { item: "paper", qty: 100, tags: ["red", "blank", "plain"], dim_cm: [ 14, 21 ] },
   { item: "planner", qty: 75, tags: ["blank", "red"], dim_cm: [ 22.85, 30 ] },
   { item: "postcard", qty: 45, tags: ["blue"], dim_cm: [ 10, 15.25 ] }
]);
```

```
// 查询 tags 等于 ["red", "blank"] 的数据
db.inventory.find( { tags: ["red", "blank"] } )

// 或者你想查询tags包含 red blank，不管它的顺序，或者是否还有其他元素，你可以使用 `$all`
db.inventory.find( { tags: { $all: ["red", "blank"] } } )

// 查询tags 里面包含 red的数据
db.inventory.find( { tags: "red" } )

// 查询dim_cm里面最少有一个值大于 25
db.inventory.find( { dim_cm: { $gt: 25 } } )

/*
** 查询dim_cm里面:
** 1. 有一个元素同时满足大于15并且小于20
** 2. 有两个分别满足小于15以及大于20
*/
db.inventory.find( { dim_cm: { $gt: 15, $lt: 20 } } )
// 所以结果是返回这四条：
{ "item" : "journal", "qty" : 25, "tags" : [ "blank", "red" ], "dim_cm" : [ 14, 21 ] }
{ "item" : "notebook", "qty" : 50, "tags" : [ "red", "blank" ], "dim_cm" : [ 14, 21 ] }
{ "item" : "paper", "qty" : 100, "tags" : [ "red", "blank", "plain" ], "dim_cm" : [ 14, 21 ] }
{ "item" : "postcard", "qty" : 45, "tags" : [ "blue" ], "dim_cm" : [ 10, 15.25 ] }

// 查询dim_cm里面至少有一个元素同时满足大于22并且小于30的数据
db.inventory.find( { dim_cm: { $elemMatch: { $gt: 22, $lt: 30 } } } )

// 查询dim_cm第二个元素大于25的数据
db.inventory.find( { "dim_cm.1": { $gt: 25 } } )

// 查询dim_cm的tags数组长度为3的数据
db.inventory.find( { "tags": { $size: 3 } } )
```

```
// 示例数据
db.inventory.insertMany( [
   { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
   { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
   { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
   { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);

// 查询instock数组里面有一个元素为{ warehouse: "A", qty: 5 }的数据，必须warehouse等于A并且qty等于5，包括顺序也必须一致
db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } )

// 查询instock数组里面至少有一个元素的qty字段值小于等于20的数组
db.inventory.find( { 'instock.qty': { $lte: 20 } } )
// 结果为：
{ "item" : "journal", "instock" : [ { "warehouse" : "A", "qty" : 5 }, { "warehouse" : "C", "qty" : 15 } ] }
{ "item" : "notebook", "instock" : [ { "warehouse" : "C", "qty" : 5 } ] }
{ "item" : "paper", "instock" : [ { "warehouse" : "A", "qty" : 60 }, { "warehouse" : "B", "qty" : 15 } ] }
{ "item" : "planner", "instock" : [ { "warehouse" : "A", "qty" : 40 }, { "warehouse" : "B", "qty" : 5 } ] }
{ "item" : "postcard", "instock" : [ { "warehouse" : "B", "qty" : 15 }, { "warehouse" : "C", "qty" : 35 } ] }

// 查询instock数组的第一个元素的qty字段小于等于20的数据
db.inventory.find( { 'instock.0.qty': { $lte: 20 } } )

// 查询instock数组里至少有一个元素同时满足qty等于5并且warehouse等于A的数组
db.inventory.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } )

// 查询instock数组里至少有一个元素的qty同时满足大于10并且小于等于20
db.inventory.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } )

// 查询instock数组里至少有一个元素满足大于10或者小于等于20
db.inventory.find( { "instock.qty": { $gt: 10,  $lte: 20 } } )
// 结果为：
{ "item" : "journal", "instock" : [ { "warehouse" : "A", "qty" : 5 }, { "warehouse" : "C", "qty" : 15 } ] }
{ "item" : "paper", "instock" : [ { "warehouse" : "A", "qty" : 60 }, { "warehouse" : "B", "qty" : 15 } ] }
{ "item" : "planner", "instock" : [ { "warehouse" : "A", "qty" : 40 }, { "warehouse" : "B", "qty" : 5 } ] }
{ "item" : "postcard", "instock" : [ { "warehouse" : "B", "qty" : 15 }, { "warehouse" : "C", "qty" : 35 } ] }

// 查询instock里任意一个元素的qty等于5并且任意一个元素的warehouse等于A的数据
db.inventory.find( { "instock.qty": 5, "instock.warehouse": "A" } )
// 结果为：
{ "item" : "journal", "instock" : [ { "warehouse" : "A", "qty" : 5 }, { "warehouse" : "C", "qty" : 15 } ] }
{ "item" : "planner", "instock" : [ { "warehouse" : "A", "qty" : 40 }, { "warehouse" : "B", "qty" : 5 } ] }
```

### 控制查询语句输出结果
默认情况下，查询语句会返回文档的所有字段，我们可以通过第二个参数来自定义返回文档里的哪些字段

关于第一个参数，我们这里就不详细说明了，可参考上面

```
// 示例数据
db.inventory.insertMany( [
  { item: "journal", status: "A", size: { h: 14, w: 21, uom: "cm" }, instock: [ { warehouse: "A", qty: 5 } ] },
  { item: "notebook", status: "A",  size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "C", qty: 5 } ] },
  { item: "paper", status: "D", size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "A", qty: 60 } ] },
  { item: "planner", status: "D", size: { h: 22.85, w: 30, uom: "cm" }, instock: [ { warehouse: "A", qty: 40 } ] },
  { item: "postcard", status: "A", size: { h: 10, w: 15.25, uom: "cm" }, instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);

// 返回所有文档的status为的A的数据的所有字段
db.inventory.find( { status: "A" } )

// 只返回文档的item, status字段以及_id（默认显示），1代表显示，0则是不显示
db.inventory.find( { status: "A" }, { item: 1, status: 1 } )

// 如果想要_id也不返回，可以这样：
db.inventory.find( { status: "A" }, { item: 1, status: 1, _id: 0 } )

// 也可以单独给某些不想显示的字段设为0，其余的正常显示
db.inventory.find( { status: "A" }, { status: 0, instock: 0 } )

// 还可以控制嵌套对象的字段，下面语句只显示_id, item, status, 以及size的uom字段
db.inventory.find(
   { status: "A" },
   { item: 1, status: 1, "size.uom": 1 }
)

// 也可以只设置不显示某个嵌套字段，下面语句不显示size的uom属性，其余正常显示
db.inventory.find(
   { status: "A" },
   { "size.uom": 0 }
)

// 控制数组中的元素某些字段是否显示
db.inventory.find( { status: "A" }, { item: 1, status: 1, "instock.qty": 1 } )
// 结果为：
{ "_id" : ObjectId("5e639b35940e1ec922af0d51"), "item" : "journal", "status" : "A", "instock" : [ { "qty" : 5 } ] }
{ "_id" : ObjectId("5e639b35940e1ec922af0d52"), "item" : "notebook", "status" : "A", "instock" : [ { "qty" : 5 } ] }
{ "_id" : ObjectId("5e639b35940e1ec922af0d55"), "item" : "postcard", "status" : "A", "instock" : [ { "qty" : 15 }, { "qty" : 35 } ] }

// 对于instock，只返回数组的最后一个元素
db.inventory.find( { status: "A" }, { item: 1, status: 1, instock: { $slice: -1 } } )

// $elemMatch, $slice 和 $ 是唯一能控制数组返回字段的方法，在这里，不可以使用类似于：{ 'instock.0': 1 }这种控制只显示instock数组的第一个元素的。
```

### 如何显示null或者缺失字段
```
// 示例数据
db.inventory.insertMany([
   { _id: 1, item: null },
   { _id: 2 }
])

// item设为null时，会返回null，以及不存在该字段的数据，比如第二条数据不存在item字段，也会被返回
db.inventory.find( { item: null } )
// 结果为：
{ "_id" : 1, "item" : null }
{ "_id" : 2 }

// 如果想要精确匹配，可以这样：
// 10 是Null类型的数值表示，具体可看：https://docs.mongodb.com/manual/reference/bson-types/，里面有各种类型对应的数值
db.inventory.find( { item : { $type: 10 } } )
// 结果为：
{ "_id" : 1, "item" : null }

// 还可以使用$exists操作符，用于标明字段是否存在
db.inventory.find( { item : { $exists: false } } )
// 结果为：
{ "_id" : 2 }
// 如果$exists设为true，则返回：
{ "_id" : 1, "item" : null }
```

### 使用游标操作数据

```
// 可以将find的返回值保存到一个变量中
var myCursor = db.users.find( { type: 2 } );

while (myCursor.hasNext()) {
   print(tojson(myCursor.next()));
}

// 可以使用printjson代替print(tojson()
var myCursor = db.users.find( { type: 2 } );

while (myCursor.hasNext()) {
   printjson(myCursor.next());
}

// 还可以使用游标的forEach方法
var myCursor =  db.users.find( { type: 2 } );

myCursor.forEach(printjson);
```

游标对象的具体有哪些方法可以看：https://docs.mongodb.com/manual/reference/method/#js-query-cursor-methods


## 更新数据

更新数据主要使用：
* `db.collection.updateOne(<filter>, <update>, <options>)`
* `db.collection.updateMany(<filter>, <update>, <options>)`
* `db.collection.replaceOne(<filter>, <update>, <options>)`

mongodb提供了更新操作符去更新数据，比如$set，具体可看文档：https://docs.mongodb.com/manual/reference/operator/update/

有一些操作符，比如$set在字段不存在时会自动添加该字段

```
// 示例数据
db.inventory.insertMany( [
   { item: "canvas", qty: 100, size: { h: 28, w: 35.5, uom: "cm" }, status: "A" },
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "mat", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
   { item: "mousepad", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "P" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
   { item: "sketchbook", qty: 80, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "sketch pad", qty: 95, size: { h: 22.85, w: 30.5, uom: "cm" }, status: "A" }
] );

// 第一个参数这里也不继续说明了，详细可以看上面查询部分

/*
** 1. 更新size的uom字段值为cm
** 2. 更新status字段值为P
** 3. 更新lastModified为当前时间，如果lastModified字段不存在，会添加该字段
*/
db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)
// 更新后的数据：
{ "_id" : ObjectId("5e6466a4940e1ec922af0d5b"), "item" : "paper", "qty" : 100, "size" : { "h" : 8.5, "w" : 11, "uom" : "cm" }, "status" : "P", "lastModified" : ISODate("2020-03-08T03:29:48.653Z") }

// 更新多条数据
db.inventory.updateMany(
   { "qty": { $lt: 50 } },
   {
     $set: { "size.uom": "in", status: "P" },
     $currentDate: { lastModified: true }
   }
)

/*
** 替换数据
** 1. _id字段不能被替换，如果新数据里包含_id字段，则值必须和原数据的一致
** 2. 新数据不能包含操作符
*/
db.inventory.replaceOne(
   { item: "paper" },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 40 } ] }
)
```

`upsert`属性
如果`updateOne()`, `updateMany()` 或者 `replaceOne()`包含`upsert: true`参数时，如果这三个方法没有匹配到任何文档，则会创建一个新的插入到集合中，如果匹配到了，则会修改或者替换原文档。

### 使用聚合更新数据
在聚合中可以使用一下更新操作符：
* $addFields
* $set
* $project
* $unset
* $replaceRoot
* $replaceWith

```
// 示例数据
db.students.insertMany([
   { _id: 1, test1: 95, test2: 92, test3: 90, modified: new Date("01/05/2020") },
   { _id: 2, test1: 98, test2: 100, test3: 102, modified: new Date("01/05/2020") },
   { _id: 3, test1: 95, test2: 110, modified: new Date("01/04/2020") }
])

/*
** updateOne方法第二个参数可以是以数组，里面可以有多种操作
** 1. 新增一个test3字段并设为98
** 2. 修改modified数据值为当前时间
*/
db.students.updateOne( { _id: 3 }, [ { $set: { "test3": 98, modified: "$$NOW"} } ] )


// 示例数据
db.students2.insertMany([
   { "_id" : 1, quiz1: 8, test2: 100, quiz2: 9, modified: new Date("01/05/2020") },
   { "_id" : 2, quiz2: 5, test1: 80, test2: 89, modified: new Date("01/05/2020") },
])

/*
** 同时执行多个操作，首先序列化所有文档（集合中的每个文档字段可能并不一致），再更新modified字段
** 1. 序列化文档，如果文档中没有 quiz1 or quiz2 or test1 or test2 就给它添加该字段，并将值设为0。该操作可以用于将集合中所有文档的字段保持一致
** 2. 更新文档的modified为当前时间
*/
db.students2.updateMany( {},
  [
    { $replaceRoot: { newRoot:
       { $mergeObjects: [ { quiz1: 0, quiz2: 0, test1: 0, test2: 0 }, "$$ROOT" ] }
    } },
    { $set: { modified: "$$NOW"}  }
  ]
)
// 结果为：
{ "_id" : 1, quiz1: 8, quiz2: 9, test1: 0, test2: 100, modified: new Date("01/05/2020") }
{ "_id" : 2, quiz1: 0, quiz2: 5, test1: 80, test2: 89, modified: new Date("01/05/2020") }

// 示例数据
db.students3.insert([
   { "_id" : 1, "tests" : [ 95, 92, 90 ], "modified" : ISODate("2019-01-01T00:00:00Z") },
   { "_id" : 2, "tests" : [ 94, 88, 90 ], "modified" : ISODate("2019-01-01T00:00:00Z") },
   { "_id" : 3, "tests" : [ 70, 75, 82 ], "modified" : ISODate("2019-01-01T00:00:00Z") }
]);

/*
** 设置average, grade字段值，并且修改modified值
** 1.1 设置average字段（如果没有会新增）为tests数组的平均值，0代表保留0个小数，即取证，具体可看$trunc操作符文档https://docs.mongodb.com/manual/reference/operator/aggregation/trunc/
** 1.2 设置modifed字段为当前时间
** 1 设置grade字段，根据average的值分别设为A,B,C,D,F
*/
db.students3.updateMany(
   { },
   [
     { $set: { average : { $trunc: [ { $avg: "$tests" }, 0 ] }, modified: "$$NOW" } },
     { $set: { grade: { $switch: {
                           branches: [
                               { case: { $gte: [ "$average", 90 ] }, then: "A" },
                               { case: { $gte: [ "$average", 80 ] }, then: "B" },
                               { case: { $gte: [ "$average", 70 ] }, then: "C" },
                               { case: { $gte: [ "$average", 60 ] }, then: "D" }
                           ],
                           default: "F"
     } } } }
   ]
)
// 结果为：
{ "_id" : 1, "tests" : [ 95, 92, 90 ], "average": 92, "grade": "A", "modified" : ISODate("2019-01-01T00:00:00Z") }
{ "_id" : 2, "tests" : [ 94, 88, 90 ], "average": 90, "grade": "A", "modified" : ISODate("2019-01-01T00:00:00Z") }
{ "_id" : 3, "tests" : [ 70, 75, 82 ], "average": 75, "grade": "C", "modified" : ISODate("2019-01-01T00:00:00Z") }

// 示例数据
db.students4.insertMany([
  { "_id" : 1, "quizzes" : [ 4, 6, 7 ] },
  { "_id" : 2, "quizzes" : [ 5 ] },
  { "_id" : 3, "quizzes" : [ 10, 10, 10 ] }
])

// 在quizzes后追加[8, 6]
db.students4.updateOne( { _id: 2 },
  [ { $set: { quizzes: { $concatArrays: [ "$quizzes", [ 8, 6 ]  ] } } } ]
)
// 结果为
{ "_id" : 2, "quizzes" : [ 5, 8, 6 ] }

// 示例数据
db.temperatures.insertMany([
  { "_id" : 1, "date" : ISODate("2019-06-23"), "tempsC" : [ 4, 12, 17 ] },
  { "_id" : 2, "date" : ISODate("2019-07-07"), "tempsC" : [ 14, 24, 11 ] },
  { "_id" : 3, "date" : ISODate("2019-10-30"), "tempsC" : [ 18, 6, 8 ] }
])

/*
** 1. 新增tempsF字段
** 2. 将tempsC数组里的摄氏度转换为华氏度 f = c * 1.8 + 32
*/
db.temperatures.updateMany( { },
  [
    { $addFields: { "tempsF": {
          $map: {
             input: "$tempsC",
             as: "celsius",
             in: { $add: [ { $multiply: ["$$celsius", 9/5 ] }, 32 ] }
          }
    } } }
  ]
)
// 结果为：
{ "_id" : 1, "date" : ISODate("2019-06-23"), "tempsC" : [ 4, 12, 17 ], "tempsF": [39.2, 53.6, 62.6] }
{ "_id" : 2, "date" : ISODate("2019-07-07"), "tempsC" : [ 14, 24, 11 ], "tempsF": [57.2, 75.2, 51.8] }
{ "_id" : 3, "date" : ISODate("2019-10-30"), "tempsC" : [ 18, 6, 8 ], "tempsF": [64.4, 42.8, 46.4] }
```

### update方法
具体可看文档：https://docs.mongodb.com/manual/reference/update-methods/

* `db.collection.updateOne()`
* `db.collection.updateMany()`
* `db.collection.replaceOne()`
* `db.collection.update()`

* `db.collection.findOneAndReplace()`
* `db.collection.findOneAndUpdate()`
* `db.collection.findAndModify()`
* `db.collection.save()`
* `db.collection.bulkWrite()`

## 删除数据
可以使用下面方法删除数据

* `db.collection.deleteMany()`
* `db.collection.deleteOne()`

```
// 示例数据
db.inventory.insertMany( [
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
] );

// 删除inventory所有数据
db.inventory.deleteMany({})

// 删除所有匹配的数据
db.inventory.deleteMany({ status : "A" })

// 删除匹配到的第一条数据
//db.inventory.deleteOne( { status: "D" } )
```

删除操作不会删除索引，即使删掉所有文档
