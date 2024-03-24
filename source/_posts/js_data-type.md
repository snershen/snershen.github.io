---
title: JavaScript - 資料型別
date: 2023-06-29 14:22:00
tags: [JavaScript]
---

JavaScript 的型別可分為兩大類，分別是原始型別與物件型別。

## 原始型別
原始型別(Primitive type)有以下七種：
- String
- Number
- Boolean
- Null
- Undefined
- Symbol：ES6/2015 之後出現
- BigInt：ES11/2020 之後出現，適用於非常大的數值。

### 字串 String
須使用雙引號 `""` 或單引號 `''` 包裹起來，且不能混合使用。
```
console.log("It's a dog."); // "It'is a dog."

console.log('It's a dog.');
// 會報錯，英文句子中重複出現單引號，導致語法錯誤

console.log("It is a dog.');
// 會報錯，混合使用單引號和雙引號，導致語法錯誤
```
如果沒有使用單引號和雙引號，JS 會認為這是變數，前面沒有宣告過該變數，就會報錯。

```js
console.log("奶油義大利麵"); // "奶油義大利麵"
console.log(奶油義大利麵);
// 會報錯，程式認為是在呼叫不存在的變數
```

### 數字 Number
JS 裡面並沒有區分整數、浮點數，所有數字都屬於 Number 型別。
其中`NaN`(not an number) 也是屬於 Number，當某值經過轉型後，該值無法轉換為數字就會出現 NaN。

### 布林 Boolean
只有 `true` 和 `false` 兩種值，它不是字串，不需要用引號包裹，經常用於流程判斷。

### Undefind 與 Null
兩者都表示空值的意思，但使用時機不同：
- `Undefined` 會出現在已宣告、尚未賦值的變數上，通常是由系統賦予，並不會手動賦值到變數上。
- `Null` 則是會手動賦予給變數，當變數儲存大量資料，需要清空釋放記憶體時，就會手動賦予 Null 到變數上。

```js
let mother;
console.log(typeof mother); // "Undefined"
```

## 物件型別
> 原始型別以外的都是物件型別(Object Type)，包括 Function、Array、Object 都是屬於物件型別，所以並沒有 Function 型別，或是 Array 型別。

### 物件 Object
使用 {} 包裹的資料，包含一對 key 和 value，可以儲存「一個對象包含多個不同性質」的資料。
```javascript
let family = {
　　father: "Mark", // father 是 key，"mark" 是 value
　　mother: "Alice",
　　dog: "Momo"
}
```

### 陣列 Array
使用 [] 包裹的資料，適合儲存「相同性質」的多筆資料。
```javascript
let drinks = ["可口可樂", "可爾必思", "珍珠奶茶"]
```

### 陣列和物件可以混合使用
```javascript
let store = {
　　boss: "Tina",
　　drinks: ["卡布奇諾", "黑咖啡", "珍珠奶茶"],
　　customer: 20
}

let stores = [
　　{
　　　　storeName: "朵朵咖啡廳",
　　　　boss: "Tina",
　　　　drinks: ["卡布奇諾", "黑咖啡", "珍珠奶茶"],
　　　　customer: 20　　
　　},
　　{
　　　　storeName: "桑心酒吧",
　　　　boss: "Gray",
　　　　drinks: ["啤酒", "長島青茶", "威士忌"],
　　　　customer: 20
　　},
]
```

## 查詢型別
### 方法一、使用 typeof 方法

印出的會是字串形式。這邊先使用在原始型別的查詢上：
```javascript
console.log(typeof "Alice"); //String
console.log(typeof 123); //Number
console.log(typeof true); //Boolean
console.log(typeof undefined); //Undefined
console.log(typeof null); //Object // why?
```

再來是物件型別的查詢：
```javascript
console.log(typeof []); //Object
console.log(typeof {}); //Object
console.log(typeof function(){}); //Function //why?
```

這邊會發現三個問題：
- null 會被判斷成 Object，這是 JS 一個著名的錯誤，原本有被提出來修正，但因為會影響到許多網站的正常運作，所以就將錯就錯了XD
- [] 和 {} 的結果都是 Object，要怎麼判斷它是陣列還是物件？
- 照理說不會出現 Function 型別，如果不屬於原始型別的，應該都會是物件型別

所以可以發現， typeof 查詢型別的結果有時候沒那麼精準，需要搭配其他方式來查詢型別。

### 方法二、Array.isArray()
以此方法來判斷型別是否為陣列，是陣列的話會回傳 true，不是就回傳 false。就可以解決使用 typeof 無法看出該值是陣列或物件的問題。
```javascript
console.log(Array.isArray([])); // true
console.log(Array.isArray({})); // false
```
### 方法三、Object.prototype.toString.call()
這方法只需要看最後結果就能夠知道確切的型別，但還是要記得 Array, Function, Object 實際上都屬於物件型別。
```javascript
console.log(Object.prototype.toString.call("Mina"));
// [object String]

console.log(Object.prototype.toString.call(123));
// [object Number]

console.log(Object.prototype.toString.call(undefined));
// [object Undefined]

console.log(Object.prototype.toString.call(null));
// [object Null]

console.log(Object.prototype.toString.call([]));
// [object Array]

console.log(Object.prototype.toString.call({}));
// [object Object]

console.log(Object.prototype.toString.call(function(){}));
// [object Function]
```
有關 JavaScript 型別的分享就到這裡，若有錯誤歡迎指正，感謝看完這篇的大家。