---
title: Pinia - Option API 版本
date: 2024-03-13 17:40:00
tags: [Pinia, Vue]
---

Pinia 是 Vue 框架的狀態管理工具，可以跨組件或跨頁面共享狀態，並且支援 Vue2、Vue3，提供了 Options API 和 Composition API，這篇主要會使用 Option API 寫法來做介紹。

# Pinia 解決了什麼問題？

Pinia 解決了「跨元件傳遞」問題。在 Vue 裡面，每個元件的資料是獨立的，不使用狀態管理工具，根元件傳到子元件會使用`props`，子元件資料傳到根元件使用`emit`。

那麼問題來了，如果要將 A 元件的資料傳到沒有從屬關係的 B 元件呢？

如果僅使用 props 和 emit，資料傳遞會變得相當複雜，使用 Pinia 就能將狀態和方法抽取出來，存到 Pinia 的 Store 裡面，再個別引入到 A、B 元件中。

# Pinia 使用說明

1. 引入 Pinia 資源
2. 在專案中加入 Pinia
3. 定義 Store
4. 使用 Store

## 一、引入 Pinia 資源

可以使用 CDN，但比較常用的方式還是 npm

### CDN：需要額外引入 VueDemi

```html
<!-- VueDemi，使用 Pinia 必要的相依套件 -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue-demi/0.13.11/index.iife.js"></script>

<!-- Pinia 網頁版，實戰中還是以 npm 為主，這是比較少見的使用方式 -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/pinia/2.0.22/pinia.iife.js"></script>
```

### npm

```javascript=
npm install pinia
```

## 二、在專案中加入 Pinia

```javascript
// 提取 Pinia 方法
const { createPinia } = Pinia;
const pinia = createPinia();
app.use(pinia);
```

## 三、定義 Store

Pinia 是使用 store 存放共用的狀態和方法，就不會像放在全域那麼容易被覆蓋或污染環境

1. 在專案的 src 資料夾裡，建立一個 store 資料夾，放置 Pinia 相關檔案，檔案命名規則為 `xxxStore.js`
2. 使用 `defineStore` 定義 store，`defineStore` 有兩個參數，第一個必須是唯一值，用來辨識專案中的 Store；第二個參數是物件，放置資料或狀態，裡面主要分成三個屬性，對應到不同用途的內容：

- `state`：類似 Vue 的 data
- `getters`：類似 Vue 的 computed
- `actions`：類似 Vue 的 methods

```javascript
// store/counterStore.js 檔案
const { defineStore } = Pinia;

// defineStore 有兩個參數
// 第一個參數必須是唯一id，用來辨識專案中的 Store
// 第二個參數是一個物件，放置資料或狀態
export default defineStore("counterStore", {
  state: () => ({ count: 0 }),
  /* 也可以這樣寫
  state: () => {
    return { count: 0 }
  },
  */
  getters: {
    double: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++;
    },
  },
});
```

## 四、使用 Store

在元件檔裡使用 Store，有兩種方式：

### 方法 1. 使用`mapState`、`mapActions`逐一取出資料與方法，比較適合內容較少的 Store

```javascript=
// counterView.vue
const { mapState, mapActions } = Pinia;
import countStore from "../store/countStore.js";

export default {
  computed: {
    // 獲取在 state 和 getters 屬性裡的內容
    ...mapState(countStore, ['state', 'double']),
  },
  methods: {
    // 獲取在 actions 屬性裡的內容
    ...mapActions(countStore, ['increment'])
  }
};
```

### 方法 2. 如果 Store 內有多個屬性，可以直接使用 `mapStores` 獲取整個 Store

```javascript=
import { mapStores } from 'pinia';

export default {
  computed: {
    ...mapStores(useCartStore, useUserStore)
  },

  methods: {
    async buyStuff() {
      if (this.userStore.isAuthenticated()) {
        await this.cartStore.buy()
        this.$router.push('/purchased')
      }
    },
  },
}
```

# 常見問題

### Q1. 為何元件中使用 mapState 時需要使用展開 (...) 的方式？

A: 以 computed 來說，若元件本身有定義內容，如果沒有使用展開語法的話，就會被 Store 的內容覆蓋掉。

### Q2. 可以將 API 請求放到 Pinia 統一管理嗎？

A: 可在 Pinia 中定義 actions 做非同步操作，操作成功時將資料帶到 state，接著在元件的 mounted 或 created 做觸發。

### Q3. 為什麼箭頭函式的 {} 還要再包一層 () 在外面？

A: 是為了讓程式解析成「這邊要回傳一個物件」，而不是「程式碼區塊」，如下方範例：

```javascript=
 export default defineStore('counterStore', {
  state: () => ({ count: 0 }),

  //等同
  state: () => {
    return { count: 0 }
  },
 })
```

# 參考資料

- [Pinia 官方文件](https://pinia.vuejs.org/)
