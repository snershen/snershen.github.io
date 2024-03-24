---
title: MongoDB Shell
date: 2024-03-17 20:10:00
tags: [MongoDB]
---

MongoDB 是一種非關聯式資料（NoSQL），談到資料庫，就會想到 CRUD 這個詞彙，指的是對資料進行的基本操作，分別是指新增(Create)、查詢(Read)、更新(Update)、移除(Delete)。

有時我們只想對資料庫做些簡單操作，不需要使用後端語言，這時就可以透過 Mongo Shell，直接在終端機裡使用指令，對資料庫進行操作。

## MongoDB 資料庫組成

可分為三個層級，這裡搭配 Excel 的概念來幫助理解：

- Database：資料庫，類似建立或打開了一個 Excel 檔案
- Collections：Excel 檔案裡的工作表
- Document：工作表裡的某一筆資料

首先會從 Database 層級，開始介紹指令。

## 資料庫

- 查看目前有哪些資料庫：`show dbs`
- 新增/切換資料庫：`use <database name>`，若存在就切換到該資料庫，不存在就新增

```js
use bookstore
// 建立一個名為 bookstore 的資料庫
```

需要留意的是，這時使用 `show dbs` 指令，還不會看到 bookstore 資料庫，因為此資料庫裡還沒有加入任何 Collections。可以想像成 Excel 新建立的檔案，預設就會開一個工作表供編輯，只是資料庫需要我們手動做這件事。

通常會指定要在某個工作表裡尋找某筆資料，所以接下來會一起介紹 Collections 和 Document 這兩個層級的指令。

## Create

### 新增單筆資料

- `insertOne(<data>, <options>)`：新增單筆資料，data 須為「物件」形式

```js
db.books.insertOne({ price: 300, category: "社會科學", name: "被討厭的勇氣" });
// 目前 db 是指 bookstore 資料庫
// 因為尚未建立任何 Collections，所以此處是建立 Collections，並命名為 books
// 透過 insertOne 指令新增單筆資料
```

### 新增多筆資料

- `insertMany(<data>, <options>)`：新增多筆資料，data 須為「陣列」形式

```js
db.books.insertOne([
  { rating: 4.5, price: 1000, name: "金錢心理學" },
  { rating: 4.5, price: 1000, name: "底層邏輯" },
]);
```

## Read

查詢通常會搭配很多語法，這邊先介紹最簡單的，列出 bookstore 資料庫中，books 工作表裡的所有資料。

- 查詢資料：`find(<filter>, <options>)`

若 find 參數為空值，就會列出所有資料，裡面也可以指定篩選條件，後續會再做進一步的介紹。

```js
db.books.find();
```

可能會覺得終端機裡呈現的格式很醜，這時候可以加入 `pretty()` 格式化資料內容：

```js
db.books.find().pretty();
```

## Update

Mongo Shell 指令裡更新資料的語法，有區分為更新該筆資料的「所有內容」和「部分內容」。

這部分有點像網路請求方法裡的 put 和 patch，兩者都可以更新資料，但前者會將該筆資料整個替換掉，後者則只更新特定欄位。

### 部分更新單筆資料

- `updateOne(<filter>, <update>, <options>)`：只會更新第一筆符合條件的資料，並更新特定欄位，通常會搭配 id 做篩選

```js
db.books.updateOne(
  { _id: ObjectId("5323442434234") },
  {
    $set: {
      rating: 3.0,
      price: 1200,
      name: "哈利波特精裝版",
    },
  }
);
```

### 部分更新多筆資料

- `updateMany(<filter>, <update>, <options>)`：更新多筆符合條件的資料，只會更新特定欄位

```js
db.books.updateMany(
  { price: 400 },
  {
    $set: {
      discount: 0.8,
    },
  }
);
```

> 以上都是部份更新資料的語法，都會搭配 $set 運算子

### 替換掉整筆資料

- `replaceOne(<filter>, <update>, <options>))`：替換掉「第一筆」符合篩選條件的資料，會更新整筆資料的內容
  ```js
  db.books.replaceOne(
    { name: "被討厭的勇氣" },
    {
      name: "被討厭的勇氣 增訂版",
    }
  );
  ```

## Delete

### 刪除單筆資料

- `deleteOne(<filter>, <options>)`：刪除單筆資料，通常會搭配 id 做篩選

```js
db.books.deleteOne({ _id: ObjectId("5323442434234") });
```

### 刪除多筆資料

- `deleteMany(<filter>, <options>)`：刪除多筆資料

```js
db.books.deleteMany({ rating: 4.8 });
```

### 刪除所有資料

- `drop()`
- `remove()`

刪除所有資料，但不會刪除索引

```js
db.books.drop();
```

```js
db.books.remove();
```

## 進階查詢

### 單筆

- `findOne()`：查詢單筆資料，通常會搭配 id，只會找到第一個符合條件的資料

```js
db.books.findOne({ _id: ObjectId("5323442434234") });
```

### 多筆

- 使用屬性查找

```js
// 回傳符合 price 欄位是 2500 的 document
db.books.find({ price: 2500 });
```

- 寫入多個篩選條件，類似邏輯運算子裡的 and

```js
db.books.deleteMany({ price: 2500, rating: 4.0 });
```

- 區間：可放入比較運算子，但是 MongoDB 在這部分不是使用 >, < 等符號，需要另外記得運算子的寫法

```js
// 查詢價格為 1500 以下，評分為 4.7 以上
db.books.find({
  price: {
    $lte: 1500,
  },
  rating: {
    $gte: 4.7,
  },
});
```

| 功能         | 運算子 |
| ------------ | ------ |
| 等於         | $eq    |
| 不等於       | $ne    |
| 大於         | $gt    |
| 小於         | $lt    |
| 大於等於     | $gte   |
| 小於等於     | $lte   |
| 存在某個值   | $in    |
| 不存在某個值 | $nin   |

- 關鍵字搜尋：只需要部分字串相同，寫成`/內容/`形式

```js
db.books.find({
  name: /豪華/,
});
```

- 保護欄位：一些欄位的隱私性較高，不希望查詢時被看見，就能夠寫入 options 參數，0 是不要顯示此欄位，1 則是可以顯示

```js
db.books.find(
  {
    name: /豪華/,
  },
  {
    _id: 0,
    rating: 0,
  }
);
```

- 篩選陣列：有時欄位裡是放入陣列資料，需要透過 $in 篩選。需要注意的是，只要其中一個條件符合，就會撈取該資料，類似 `$or` 邏輯運算子

```js
db.books.find({
  payment: {
    $in: ["信用卡", "ATM"],
  },
});
```

### 邏輯運算子

通常有多重條件，會使用陣列形式

- `$and`：符合所有條件

```js
db.books.find({
  $and: [{ likes: { $gte: 1500 } }, { comments: { $gte: 1500 } }],
});
```

- `$or：符合其中一個欄位`

```js
db.books.find({
  $or: [{ likes: { $gte: 1000 } }, { comments: { $gte: 1000 } }],
});
```

## 操作陣列

- `$push`：陣列新增資料

```js
db.books.updateOne(
  { _id: ObjectId("65f6f3504039b40bcfe20369") },
  {
    $push: {
      tags: "遊記",
    },
  }
);
```

- `$addToSet`：陣列新增資料，若陣列已經有該資料就不會新增

```js
db.books.updateOne(
  { _id: ObjectId("65f6f3504039b40bcfe20369") },
  {
    $addToSet: {
      tags: "遊記",
    },
  }
);
```

- `$pull`：移除陣列中特定資料

```js
db.books.updateMany(
  {},
  {
    $pull: {
      tags: "感情",
    },
  }
);
```

## 其他指令

- 計算 document 數量：`find().count()`

```js
db.books.find().count();
```

- 查詢＋排序

```js
// 時間由近到遠
db.books.find({ category: "社會科學" }).sort({ createdAt: -1 });

// 時間由遠到近
db.books.find({ category: "社會科學" }).sort({ createdAt: 1 });
```

- 限定取回的資料筆數：`limit()`

```js
db.books.find({ category: "社會科學" }).limit(30);
```

- 跳過 x 筆資料：`skip()`

```js
db.books
  .find({ comments: { $gt: 100 } })
  .skip(30)
  .limit(30);
```
