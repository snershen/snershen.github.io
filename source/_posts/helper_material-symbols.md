---
title: Material Symbols 使用攻略
date: 2023-07-05 14:10:00
tags: [第三方資源, Icon]
---

開發網頁時經常會使用到 Google 的 Material Symbols 服務，但大多都是直接引入預設設定，頂多透過官方提供的簡易編輯器，調整 icon 樣式。

在仔細看了官方文件之後，發現還有一些設定可以在程式碼中調整，使用上更加彈性，因此寫了這篇文章記錄下來。Material Symbols 有分成三個基本類型，分別為：

- Outlined：一般
- Rounded：線條較圓潤
- Sharp：線條較尖銳

並附上各類別的 CDN（文章主要會以 Outlined 類別來示範）：

```html
// Outlined
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0" />

// Rounded
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Rounded:opsz,wght,FILL,GRAD@24,400,0,0" />

// Sharp
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Sharp:opsz,wght,FILL,GRAD@24,400,0,0" />
```

可以看到 href 屬性裡的最後方有一些參數 `opsz,wght,FILL,GRAD@24,400,0,0`，後面也會解釋這段的用途。如果使用 Material Symbols 時曾有過以下疑問，這篇都會提到：

- FILL, wght, GRAD, opsz 這些東西代表什麼意義？
- 在一個網頁中如何同時引入填滿與線框樣式的 icon？

接下來就讓我們進入第一個問題，來了解 FILL, wght, GRAD, opsz 等參數。

## Material Symbols 各個軸線的意義

在 Material Symbols 中控制 icon 樣式，主要透過以下四個軸線：

### FILL 軸：控制 icon 樣式是否填滿
- 數值只有 0 和 1，0 代表無填滿，1 代表填滿的 icon
- 預設值為 0

### wght 軸：控制筆畫粗細
- 數值範圍介於 100~700 之間，數字越大筆畫越粗
- 會影響到符號的整體大小

### GRAD 軸
- 數值介於 -25~200 之間

實際操作過覺得蠻像效果沒那麼顯著的 wght，查看官方文件有提到使用時機：
- -25：可用在深色背景，減少眩光
- 200：讓符號更佳醒目

### opsz 軸
- 數值介於 20dp 到 48dp 之間
- 筆畫粗細會自動跟著圖示大小改變

如果想實際查看各個軸線調整後的效果，[官方網站](https://fonts.google.com/icons)有提供編輯器，可以調整數值看看效果。

## Material Symbols 引入方式
有三種方法，差別在於是否需要在程式碼中改變 icon 樣式，與下載到本地使用。
- Google Fonts API - 使用預設設定（無法在程式碼變更 icon 樣式）
- Google Fonts API - 載入變數字體
- 下載到本地

### 方法一：Google Fonts API - 使用預設設定
這種方法可以先在 Google Fonts 提供的編輯器做微調，但引入到程式碼中就無法進行更改，若網頁中全部 icon 都是相同樣式，可以採用該方法。只需要在 HTML 文件的 <head> 裡放入以下語法，即完成引入：
```html=
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0" />
```
若沒有做任何微調，各個軸線的預設值分別為（也可以觀察上方的連結數值）：
- wght：400
- GRAD：0
- FILL：0
- opsz：24

### 方法二：Google Fonts API - 載入變數字型
此方式可以在程式碼中透過這四個軸，個別設定 icon，當網頁中的 icon 有不同樣式，建議使用此方法。一樣在 HTML 裡的 <head> 標籤放入以下語法，即完成引入：
```html=
<link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200" rel="stylesheet" />
```
上面網址裡最後出現的 number..number，代表的是數值使用範圍，例如 20..48，表示的就是 20~48 數值皆可使用，且對應的是 opsz 軸。

可以與方法一的網址比較看看不同：
```javascript
// 方法一
opsz,wght,FILL,GRAD@24,400,0,0

// 方法二
opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200
```

### 方法三：下載到本地
1. 到[官方 Github](https://github.com/google/material-design-icons/tree/master/variablefont)下載需要的字型檔案
2. 在 CSS 中引入檔案
```css=
@font-face {
  font-family: 'Material Symbols Outlined';
  font-style: normal;
  src: url(/material-symbols.woff) format('woff');
}
```
3. 額外在 CSS 中寫入樣式：因為前面兩個方法是從官方提供的 API 直接引入 icon，預設已帶有這些設定，現在要自行下載到本地使用，所以設定要另外自己加上去。如果沒有以下設定，Material Symbols 會跑不出來。
```css=
.material-symbols-outlined {
  font-family: 'Material Symbols Outlined';
  font-weight: normal;
  font-style: normal;
  font-size: 24px;  /* Preferred icon size */
  display: inline-block;
  line-height: 1;
  text-transform: none;
  letter-spacing: normal;
  word-wrap: normal;
  white-space: nowrap;
  direction: ltr;
}
```

介紹到這邊，大致上可以了解 Material Symbols 的設定和使用方式，下面我們來一個狀況題吧。

## 狀況題：在同一網頁載入填滿 / 線框 icon
這邊使用「方法二 Google Fonts API - 載入變數字型」來進行
1. 引入變數字型
```html=
<link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200" rel="stylesheet" />
```
2. 在程式碼中自定義樣式，這步驟有兩種做法，使用其中一種就可以囉

### 寫法一：在 HTML 標籤的 style 屬性寫入 Google Fonts 的軸線設定
```html=
// 直接針對父容器做設定，裡面的 icon 都會一起吃到樣式
// 注意這裡的 'FILL' 數值
<div style="font-variation-settings: 'FILL' 0, 'wght' 100, 'GRAD' 0, 'opsz' 48;">
  <span class="material-symbols-outlined">search</span>
  <span class="material-symbols-outlined">home</span>
  <span class="material-symbols-outlined">settings</span>
  <span class="material-symbols-outlined">favorite</span>
</div>

// 注意這裡的 'FILL' 數值
<div style="font-variation-settings: 'FILL' 1, 'wght' 100, 'GRAD' 0, 'opsz' 48;">
  <span class="material-symbols-outlined">search</span>
  <span class="material-symbols-outlined">home</span>
  <span class="material-symbols-outlined">settings</span>
  <span class="material-symbols-outlined">favorite</span>
</div>　　
```

### 寫法二：使用 CSS 樣式寫入設定
- HTML 檔案：原本樣式是使用 inline style 寫法，改為寫入 class 屬性，樣式設定都放到 CSS 檔案中
```html=
<div class="outline-icon">
  <span class="material-symbols-outlined">search</span>
  <span class="material-symbols-outlined">home</span>
  <span class="material-symbols-outlined">settings</span>
  <span class="material-symbols-outlined">favorite</span>
</div>

<div class="fill-icon">
  <span class="material-symbols-outlined">search</span>
  <span class="material-symbols-outlined">home</span>
  <span class="material-symbols-outlined">settings</span>
  <span class="material-symbols-outlined">favorite</span>
</div>
```

- CSS 檔案：注意軸線的大小寫一定要相同，不然會失效，像是 'FILL' 不能寫成 'fill'
```scss=
.outline-icon{
　 font-variation-settings: 'FILL' 0;
　 // 如果要同時寫入多個設定
　 // font-variation-settings: 'FILL' 0, 'wght' 700;
}

.fill-icon{
   font-variation-settings: 'FILL' 1;
}
```

這種寫法甚至能夠搭配 animation 語法使用，做一些簡單的動畫，可參考[官方文件](https://developers.google.com/fonts/docs/material_symbols?hl=zh-tw#variable_font_with_google_fonts)的說明嘗試看看。

有關 Google 提供的開源圖示 Material Symbols 的分享就到這裡，感謝看完這篇的大家。