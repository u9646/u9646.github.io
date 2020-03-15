---
title: mongodb Query 和 Projection 常用操作符
date: 2020-03-08 20:02:19
tags:
 - mongodb
---

> [文档链接](https://docs.mongodb.com/manual/reference/operator/query/)

## 比较运算符

### $eq
`{ <field>: { $eq: <value> } }`
选择：等于某个值

### $gt
`{field: {$gt: value} }`
选择：大于某个值

### $gte
`{field: {$gte: value} }`
选择：大于等于某个值

### $in
`{ field: { $in: [<value1>, <value2>, ... <valueN> ] } }`
选择：
1. filed是一个值，并且等于value1 or value2 ...
1. field是个数组，并且filed中至少存在某个值等于value1 or value2 ...

### $lt
`{field: {$lt: value} }`
选择：小于某个值

### $lte
`{ field: { $lte: value} }`
选择：小于等于某个值

### $ne
`{field: {$ne: value} }`

选择：
1. field不等于某个值
2. field不存在

### $nin
`{ field: { $nin: [ <value1>, <value2> ... <valueN> ]} }`

选择：
1. field值不存在于数组中
2. field不存在

## 逻辑运算符
### $and
`{ $and: [ { <expression1> }, { <expression2> } , ... , { <expressionN> } ] }`

选择同时匹配所有expression的数据

> 如果expression1为false，则mongodb不会继续后续的判断

### $not
`{ field: { $not: { <operator-expression> } } }`

选择所有不符合operator-expression的数据。

```
// 选择所有price小于等于1.99或者price字段不存在的数据
db.inventory.find( { price: { $not: { $gt: 1.99 } } } )
```

### $nor
`{ $nor: [ { <expression1> }, { <expression2> }, ...  { <expressionN> } ] }`

选择同时不满足所有expression的数据

### $or
`{ $or: [ { <expression1> }, { <expression2> }, ... , { <expressionN> } ] }`

选择至少满足一个expression的数据

## 元素运算符
### $exists
`{ field: { $exists: <boolean> } }`

选择filed是否存在的数据，$exists: true代表存在，false代表不存在

### $type
`{ field: { $type: <BSON type> } }`
选择filed是某个具体类型的实例的数据，可以写具体type或者对应数字

具体BSON type可以看文档：https://docs.mongodb.com/manual/reference/operator/query/type/#document-type-available-types

## 数组运算符
### $all
`{ <field>: { $all: [ <value1> , <value2> ... ] } }`

选择filed是个数组，并且包含所有value1 and value2 ...

### $elemMatch
`{ <field>: { $elemMatch: { <query1>, <query2>, ... } } }`

选择filed是个数组并且至少有一个元素满足query1 or query2 ...

> 如果query只有一个，那么你不需要使用$elemMatch

### $size
`db.collection.find( { field: { $size: 2 } } );`

选择filed数组长度等于某个值的数据

## projection运算符

### $
```
// 示例数据
{ "_id" : 1, "semester" : 1, "grades" : [ 70, 87, 90 ] }
{ "_id" : 2, "semester" : 1, "grades" : [ 90, 88, 92 ] }
{ "_id" : 3, "semester" : 1, "grades" : [ 85, 100, 90 ] }
{ "_id" : 4, "semester" : 2, "grades" : [ 79, 85, 80 ] }
{ "_id" : 5, "semester" : 2, "grades" : [ 88, 88, 92 ] }
{ "_id" : 6, "semester" : 2, "grades" : [ 95, 90, 96 ] }

// 只返回grades数组的第一个元素
db.students.find( { semester: 1, grades: { $gte: 85 } },
                  { "grades.$": 1 } )
// 结果为：
{ "_id" : 1, "grades" : [ 87 ] }
{ "_id" : 2, "grades" : [ 90 ] }
{ "_id" : 3, "grades" : [ 85 ] }

// { "grades.$": 1 } 这里的1只是代表显示grades字段
```

### $elemMatch
用于控制数据返回值，只会显示某个字段第一个满足$elemMatch的元素

```
// 示例数据
{
 _id: 1,
 zipcode: "63109",
 students: [
              { name: "john", school: 102, age: 10 },
              { name: "jess", school: 102, age: 11 },
              { name: "jeff", school: 108, age: 15 }
           ]
}
{
 _id: 2,
 zipcode: "63110",
 students: [
              { name: "ajax", school: 100, age: 7 },
              { name: "achilles", school: 100, age: 8 },
           ]
}
{
 _id: 3,
 zipcode: "63109",
 students: [
              { name: "ajax", school: 100, age: 7 },
              { name: "achilles", school: 100, age: 8 },
           ]
}
{
 _id: 4,
 zipcode: "63109",
 students: [
              { name: "barney", school: 102, age: 7 },
              { name: "ruth", school: 102, age: 16 },
           ]
}

db.schools.find( { zipcode: "63109" },
                 { students: { $elemMatch: { school: 102 } } } )
// 结果为：
{ "_id" : 1, "students" : [ { "name" : "john", "school" : 102, "age" : 10 } ] }
{ "_id" : 3 }
{ "_id" : 4, "students" : [ { "name" : "barney", "school" : 102, "age" : 7 } ] }



db.schools.find( { zipcode: "63109" },
                 { students: { $elemMatch: { school: 102, age: { $gt: 10} } } } )
// 结果为：
{ "_id" : 1, "students" : [ { "name" : "jess", "school" : 102, "age" : 11 } ] }
{ "_id" : 3 }
{ "_id" : 4, "students" : [ { "name" : "ruth", "school" : 102, "age" : 16 } ] }
```

### $slice
`db.collection.find( { field: value }, { array: {$slice: count } } )`

用于控制某个数组字段返回多少个元素

```
// 返回comments前5个元素
db.posts.find( {}, { comments: { $slice: 5 } } )

// 返回最后5个元素
//db.posts.find( {}, { comments: { $slice: -5 } } )
```

$slice还可以是个数组，即[skip, limit]，表明跳到第skip个元素，从这里选择limit个。

```
// 跳过前20个元素，然后开始往后选择10个
db.posts.find( {}, { comments: { $slice: [ 20, 10 ] } } )

// 从倒数第20个元素开始，往后选择10个元素
db.posts.find( {}, { comments: { $slice: [ -20, 10 ] } } )
```

还有一些其他操作符，可以参考文档。
