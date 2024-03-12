---
title: mongoDB 資料庫（資料表操作）
date: 2024-03-10 20:51:16
tags:
  - MongoDB
  - Mongo Shell
categories:
  - 前端
description: 用自己的口語，再次筆記練習 mongoDB 的資料庫與資料表操作
cover: https://i.imgur.com/R9qXjKc.jpeg
---

:::tip
關於 **mongoDB 資料庫與資料表操作**
用自己的口語再次筆記練習
:::

## 前置操作

（需要先安裝 MongoDB、Mongo Shell、MongoDB Compass）

### 使用 mongod 開啟伺服器應用（資料庫）
```bash
mongod --dbpath `db資料夾路徑` --logpath `log資料夾路徑`
```

### mongosh：造訪資料庫，並做一些應用（新增、刪除、修改…）

```bash
mongosh
```

![mongosh](https://i.imgur.com/VZGNBQh.png)

### show dbs：呈現現有資料庫（一開始預設是三個）

```tsx
show dbs
```

### use 來使用某個資料庫

如果沒有這個資料庫，則會直接幫我們新增，並移動到這個資料庫

```bash
use `資料庫名稱`
// use hotel
```

---

## 資料表操作單筆、多筆資料(document)

### insert 相關

**insertOne 新增單筆資料：** 在 posts 這個資料表中，新增一筆資料(document)，這筆資料有七個欄位（如果一開始沒有 posts 這個資料表，則會在輸入指令時幫我們新增）
```bash
db.posts.insertOne({
    "name":"Billy",
    "image":"https://dummyimage.com/248x100.png/dddddd/000000",
    "content":"我是貼文內容，20240304新增",
    "likes":50,
    "comment":30,
    "type":"group",
    "tags":["心情","教育"],
})
```

**insertMany 新增多筆資料：**在 posts 這個資料表中，新增兩筆資料(document)，每筆資料有七個欄位

```bash
db.posts.insertMany([
	{
    "name":"Billy2",
    "image":"https://dummyimage.com/248x100.png/dddddd/000000",
    "content":"我是貼文內容222，20240304新增",
    "likes":80,
    "comment":60,
    "type":"group",
    "tags":["心情","教育"],
  },
    {
    "name":"Billy3",
    "image":"https://dummyimage.com/248x100.png/dddddd/000000",
    "content":"我是貼文內容333，20240304新增",
    "likes":500,
    "comment":300,
    "type":"group",
    "tags":["心情","教育"],
  }
])
```

### Update 相關

**updateOne 修改單筆資料：**在 posts 這個資料表中，根據條件篩選出的一筆資料(document)，進行欄位的調整。下方範例是將篩選出的資料(document)，將 content 欄位的值調整為”測試資料”

```bash
db.posts.updateOne(
    {"_id":ObjectId("xxxxxxxxx...")}, // 條件篩選
    {$set:{"content":"測試資料"}}
)
```

篩選一筆特定資料(document)，將裡頭的 tags 陣列欄位，再新增一個新 tags 

```bash
db.posts.updateOne(
{"_id":ObjectId("65dcxxx...")}
,{$push:{tags:"遊記"}})

// 驗證
// db.posts.find({ 
   // "_id":ObjectId("65dcxxx...")
// })

```

**updateMany 修改多筆資料：**在 posts 這個資料表中，去篩選出多筆 name 欄位為"abc123"的資料(document)，並將這些資料的 content 欄位都調整成"測試"

```bash
db.posts.updateMany(
  {"name":"abc123"},
  {$set:{"content":"測試"}}
)

```

使用 updateMany 來移除欄位，下方範例是將 tags 陣列的"感情" tag 都移除

```bash
db.posts.updateMany(
  { "tags": "感情" },
  { $pull: { "tags": "感情" } }
)

```

### delete 相關

**deleteOne 刪除單筆資料：**在 posts 這個資料表中，去篩選 id 為 aaa123 的資料(document)，然後將之刪除

```bash
db.posts.deleteOne(
  {"_id":ObjectId("aaa123")}
)

```

**deleteMany 刪除多筆資料：**將 type: group 這個條件中的多筆資料都刪除

```bash
db.posts.deleteMany(
  {
    "type":"group" // 條件
  }
)

```

多個條件：將名字為 abc，以及 likes 數量 500(含) 以下都刪除

```bash
db.posts.deleteMany(
  {
    "name":"abc",
    "likes":{
      $lte: 500 // $lte 表示小於等於
    }
  }
)

```

不寫任何條件，等同於刪除全部資料

```bash
db.posts.deleteMany({})
```

### find 相關

查詢全部 posts 的資料列表(document) 

```bash
db.posts.find()
db.posts.find().pretty()
```

模糊搜尋：name 欄位含有 o 的資料列表

```bash
db.posts.find(
  {"name":/o/}
)

```

單個條件：查詢 comments 有大於等於 500 以上的資料列表

```bash
db.posts.find(
  {
    "comments": {
        $gte:500
    },
  }
)

```

查詢 image 欄位為 null 的資料列表

```bash
db.posts.find({
  "image": null
})

```

多個條件：name 欄位為 abc123，按讚數大於等於 500、小於等於 1000

```bash
db.posts.find(
  {
    "name": "abc123",
    "likes": {
        $gte:500, // $gte 大於等於
        $lte:1000 // $lte 小於等於
    },
  }
)
```

project 保護欄位：查詢 tags 欄位中，有包含到"幹話"的資料列表，然後將 _id 欄位設置隱藏（在有些狀況中，我們不可透露重要資訊在 user）

```bash
db.posts.find(
  {
    "tags": {
      $in: ['幹話'] // $in 表示內容存在某個值
    }
  },
  {"_id": 0} // 將 id 欄位隱藏
)

```

查詢 tags 欄位，有 謎因 或(or) 幹話 的資料列表

```bash
db.posts.find(
  {
    "tags": {
      $in: ['謎因','幹話']
    }
  }
)

```

find 的 $and 與 $or：
在 posts 資料表中，找尋 likes 跟(and) comments 欄位都大於等於 1500 以上的資料列表（[參考連結](https://www.runoob.com/mongodb/mongodb-query.html)）

```bash
db.posts.find({
  $and: [
    { "likes": { $gte: 1500 } },
    { "comments": { $gte: 1500 } }
  ]
})

//db.posts.find({ 這個範例也可以這樣寫
  //"likes": { $gte: 1500 },
  //"comments": { $gte: 1500 }
//})
```

在 posts 資料表中，找尋 likes 或(or) comments 欄位中，大於等於 1000 以上的資料列表

```bash
db.posts.find({
  $or: [
    { "likes": { $gte: 1000 } },
    { "comments": { $gte: 1000 } }
  ]
})

```

顯示 posts 資料表的資料筆數（數字）（[參考連結](https://www.mongodb.com/docs/manual/reference/method/db.collection.countDocuments/)）

```bash
db.posts.countDocuments()
```

查詢 name 欄位為 abc123 的資料列表，而這個排序從新到舊（[參考連結](https://www.runoob.com/mongodb/mongodb-sort.html)）

```bash
db.posts.find(
  { "name": "abc123" } // 查詢條件
).sort(
  { "createdAt": -1 } // 根據 createdAt 欄位進行降序排序 (由新到舊)
)

```

查詢 name 欄位為 abc123 的資料列表，只顯示前 30 筆資料（[參考連結](https://www.runoob.com/mongodb/mongodb-limit-skip.html)）

```bash
db.posts.find(
  { "name": "abc123" } // 查詢條件
).limit(30) // 限制返回結果為前 30 筆資料

```

查詢 name 欄位為 abc123、likes 欄位大於等於 100 的資料列表。而這個資料列表只需顯示前 30 筆，並且對於這資料列表做新到舊的排序

```bash
db.posts.find(
  { // 條件
    "name": "abc123",
    "likes": {
        $gte:100,
    }
  }
).limit(30).sort( {"createdAt": -1} ); // 筆數與排序

```

查詢 comments 超過 100 的資料列表，取得時先跳過前 30 筆資料，再顯示 30 筆資料

```bash
db.posts.find(
  {
    "comments": {
        $gt: 100
    }
  }
).skip(30).limit(30); // 要 30 筆，但先跳過 30 筆
```

### replace 相關

替換 **replaceOne、replaceMany：**如下範例，透過 id 篩選了一筆要 replace 的資料，這邊會將 name 欄位修改成 yes~。
假設這筆資料還有其它欄位的話，在 replace 更新未寫到時，則會視為欄位的刪除，也就是這筆資料會只剩下 id 跟 name 欄位。（replace 這樣的用法也有刪除的功能）

```bash
db.posts.replaceOne(
{"_id":ObjectId("abcxxx")},
{name:"yes~"})
```

**Comparison Query Operators 表格**

| $eq | 等於 |
| --- | --- |
| $ne | 不等於 |
| $gt | 大於 |
| $lt | 小於 |
| $gte | 大於等於 |
| $lte | 小於等於 |
| $in | 存在某個值 |
| $nin | 不存在某個值 |