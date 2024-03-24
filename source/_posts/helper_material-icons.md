---
title: Material Icons 引入攻略
date: 2023-08-22 12:09:00
tags: [第三方資源, Icon]
---

Material Icons 是 Google 提供的免費圖示資源，主要分成五種類別，分別是 Filled、Outlined、Rounded、Sharp、Two tone。

但官方文件僅提到 Filled 類別的引用方式，沒有提到其他類別的 CDN 與 font-family 的設定，本文將會詳細紀錄這些部分，另外還會提到如何透過偽元素加入 Material Icons。

## 步驟一：使用 CDN ​引入 Material Icons
### 1. 在 `<head>` 引入該類別的 CDN
官方文件主要以 Filled 類別做示範，沒有提到其他類別的 CDN 路徑，筆者嘗試過後發現規則，是在**原來的 CDN 連結後面加入 [+類別名稱]（但 Rounded 是例外）**，紀錄如下：

- Filled：不用加入任何類別，預設就是 Filled 類別的 icon
```html
<link
  href="https://fonts.googleapis.com/icon?family=Material+Icons"
  rel="stylesheet"
/>
```

- Outlined
```html
<link
  href="https://fonts.googleapis.com/icon?family=Material+Icons+Outlined"
  rel="stylesheet"
/>
```

- Rounded：要特別注意是加入 Round，不是 Rounded
```html
<link
  href="https://fonts.googleapis.com/icon?family=Material+Icons+Round"
  rel="stylesheet"
/>
```

- Sharp
```html
<link
  href="https://fonts.googleapis.com/icon?family=Material+Icons+Sharp"
  rel="stylesheet"
/>
```

- Two tone：需要拆開兩個單字，再加入到 CDN 裡
```html
<link
  href="https://fonts.googleapis.com/icon?family=Material+Icons+Two+Tone"
  rel="stylesheet"
/>
```

### 2. 到 Google Icons 找尋指定 icon
前往 [Material Icons 官網](https://fonts.google.com/icons?icon.set=Material+Icons)，點擊 icon 後會開啟右方的面板，複製紅框處的程式碼，就能接續下個步驟引入到自己的網頁中，兩個方法擇一即可。

## 步驟二：引入到 HTML 中

### 方法1：直接貼入程式碼
```html
<span class="material-icons-outlined"> search </span>
```

### 方法2：透過偽元素寫入到 HTML 中

很適合用在有大量重複的 icon，例如列表圖案。這裡的`font-family`是重點，如果沒有寫入正確的值就不會有效果，但在官方文件中也是除了 Filled 之外，沒有提到不同類別的 font-family的命名，測試後整理如下：

- Filled:`"Material Icons"`
- Outlined:`"Material Icons Outlined"`
- Sharp:`"Material Icons Sharp"`
- Rounded:`"Material Icons Round"`
- Two tone:`"Material Icons Two Tone"`

另外偽元素的 content 要寫入 Icon font 欄位裡的名稱，如下：

```html
.list-icon li::before {
  content: "search";
  font-family: "Material Icons Outlined";
}
```

## 成果
這邊也附上完整的程式碼，供大家複製過去看看效果～

```html
<html lang="zh-Hant">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons+Outlined" rel="stylesheet" />
    <style>
      .list-icon li::before {
        content: "lightbulb";
        font-family: "Material Icons Outlined";
      }
    </style>
  </head>
  <body>
    <span class="material-icons-outlined"> search </span>
    <ul class="list-icon">
      <li>項目1</li>
      <li>項目2</li>
      <li>項目3</li>
    </ul>
  </body>
</html>
```

## 常見問題
### Q.如何放大 Icon？
A: 使用 `font-size` 屬性就可以囉！

### Q.為什麼無法成功引入 Material Icons？
A: 可以檢查以下幾個部分：
1. CDN 有無引入，以及引入的類別是否正確
2. material-icons 的 class 是否正確
3. 如果有使用偽元素，必須確認 `font-family` 屬性是否設定正確、`content` 屬性有寫入正確的 icon 名稱

### Q.Material Icons 和 Material Symbols 有什麼差別？
A: 都是由 Google 推出，但 Material Icons 出現時間更早，且圖示為固定樣式，不能設定線條粗細、是否填滿等屬性；Symbols 則是可以透過參數修改部分設定，之後也會寫一篇介紹 Material Symbols 的使用方式。

<br>

今天就介紹到這裡，希望大家都能順利引入！