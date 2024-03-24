---
title: Vue Router - 實現 SPA 架構
date: 2024-02-15 10:09:00
tags: [Vue, Router]
---


Vue Router 是由官方提供的前端路由管理工具，路由又可分為前端、後端路由。本文章介紹的 Vue Router 會著重在由前端模擬的路由，以實現 SPA 架構，

## 前端路由與後端路由

兩者的主要差別是網址格式、頁面渲染方式。

### 網址格式

- 前端路由網址會有`#`符號：`https://www.store.com/#/user`
- 後端路由網址：`https://www.store.com/user`

### 頁面渲染方式

#### 後端路由：完整的頁面往前端傳遞

1. 瀏覽器發出請求給伺服器
2. 首先 Router 會判斷使用者進入的路由，並告知 Controller 要取得的資料
3. 接著 Controller 透過 Model 從資料庫撈出資料，同時從 View 取得相關版型，最後將資料渲染到頁面上，並回傳給伺服器
4. 伺服器再把完整的頁面渲染給瀏覽器

#### 前端路由：由前端模擬的路由，同一區塊在不同頁面時「不會」重新渲染

1. 透過網址指定路由後
2. 查找路由表裡，該路由對應的檔案路徑
3. 接著渲染到畫面上

接下來介紹 Vue Router 的使用方式。

## Vue Router 使用流程

可以在純 HTML 中使用，或是構建工具 Vite、Vue cli 中使用，簡化步驟如下：

1. 引入 Vue Router 資源
2. 定義元件
3. 定義路由表
4. 加入對應連結

> 建議都先將元件建好後，再放到路由表裡，較容易偵錯

下面分別說明純 HTML 和搭配 Vite 構建工具的詳細操作：

### 使用方式一、純 HTML 版本

1. 引入 Vue Router

- 使用 CDN 引入 Vue Router，這樣在純 HTML 也可以使用
- 記得引用位置要放在 Vue 之後

```html
<div id="app">
  <h2>路由示範</h2>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script src="https://unpkg.com/vue-router@4.0.15/dist/vue-router.global.js."></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        counter: 0,
        text: "這裡有一段文字",
      };
    },
  });

  //路由設定
  const router = VueRouter.creteRouter({
    // 網址路徑模式，使用網址 hash 的形式
    // createWebHashHistory 代表路由是由前端模擬
    history: VueRouter.createWebHashHistory(),

    // 匯入路由表
    router: [],
  });

  app.use(router);
  app.mount("#app");
</script>
```

2. 定義元件與路由表

```javascript=
const componentA = {
  template: `<div>A</div>`,
};

const componentB = {
  template: `<div>B</div>`,
};

const router = VueRouter.creteRouter({
  history: VueRouter.createWebHashHistory(),
  router: [
    {
      //定義路徑
      path: "/a",

      //定義元件
      component: componentA,
    },
    {
      path: "/b",
      component: componentB,
    },
  ],
});
```

3. 加入對應連結，供使用者點擊

使用 `<router-link>` 標籤，to 屬性則是寫入路由表的 path 路徑

```html
<div id="app">
  <router-link to="/a">a</router-link>
  <router-link to="/b">b</router-link>
  <router-view></router-view>
</div>
```

### 使用方式二、搭配 Vite

1. 路由表寫入以下內容，會放在 router 資料夾裡的 index.js 檔案

   ```javascript=
   import { createRouter, createWebHashHistory } from "vue-router";
   import Home from "../views/Home.vue";

   const router = createRouter({
     history: createWebHashHistory(import.meta.env.BASE_URL),
     routes: [
       {
         path: "/",
         name: "首頁",
         component: Home,
       },
     ],
   });

   export default router
   ```

2. 在 main.js 引入 Vue Router

   ```javascript=
   import { createApp } from "vue";
   import App from "./App.vue";
   import router from "./router";

   const app = createApp(App);

   app.use(router);
   app.mount("#app");
   ```

3. 到 App.vue 檔案中放入 `<router-view>` 標籤

   ```html
   <template>
     <div>
       <router-link to="/">Home</router-link>
       <router-link :to="{ name: '首頁' }">Home</router-link>
     </div>
     <router-view />
   </template>
   ```

## 路由表常見屬性

- `path`：寫入路由，第一層的 path 屬性最前方必須加上 `/`，例如 /product
- `name`：可以寫入中文名稱，也可用來切換路由，使用方式如下：

```html
<template>
  <div>
    <!-- 直接寫入路由 -->
    <router-link to="/">Home</router-link>
    <!-- 動態寫入 name 屬性 -->
    <router-link :to="{ name: '首頁' }">Home</router-link>
  </div>
  <router-view />
</template>
```

- `component`：寫入要渲染的元件（該元件通常放置在 views 資料夾中），可以透過 import 定義的名稱，或是使用箭頭函式

```javascript
// 箭頭函式
{
  path: '/product',
  name: '產品頁面',
  component: () => import('../views/Product.vue')
}
```

```javascript=
// import 方式
import Product from '../views/Product.vue'

//... 省略
{
  path: '/product',
  name: '產品頁面',
  component: Product
}
//... 省略
```

- `children`：陣列形式，可在路由裡再放入子路由，裡面的 path 屬性不用寫入 `/`

## Vue 常見應用

### 應用一、巢狀路由

適合使用在當多個頁面裡有共用元件，只想要切換部分畫面時

1. 建立子元件
2. 在路由表新增巢狀路由表，使用 `children` 屬性引入子元件

```javascript=
const router = VueRouter.creteRouter({
  history: VueRouter.createWebHashHistory(),
  router: [
    {
      path: "/newPage",
      component: "../views/NewPage.vue",
      children: [
        {
          path: "a",
          component: componentA,
        },
        {
          path: "b",
          component: componentB,
        },
      ],
    },
  ],
});
```

3. 在外層元件加入 `<router-view>` 和 `<router-link>`

```html=
<template>
  <div class="row">
    <div class="col-4">
      <router-link to="/newPage/a"></routerLink>
      <router-link to="/newPage/b"></routerLink>
    </div>
    <div class="col-8">
      <router-view></router-view>
    </div>
  </div>
</template>
```

### 應用二、具名視圖

一般情況下，一個元件裡面只有一個 router-view，但如果要在同一個元件下引入多個 router-view，就需要使用具名視圖的手法

1. 建立子元件
2. 在元件中放入多個 <router-view>，會需要知道哪個元件是放在哪個 router-view，需要寫入 `name` 屬性，該屬性之後會用來識別子元件要載入的 router-view

```html
<template>
  <div class="row">
    <div class="col-6">
      <router-view name="left"></router-view>
    </div>
    <div class="col-6">
      <router-view name="right"></router-view>
    </div>
  </div>
</template>
```

3. 路由表中在元件裡透過 `children` 屬性引入子元件
   - 並在子元件裡寫入 `components` 屬性，值是一個物件
   - 物件屬性寫入先前命名的 name 屬性值，並引入對應的子元件檔案

```javascript=
const router = VueRouter.creteRouter({
  history: VueRouter.createWebHashHistory(),
  router: [
    {
      path: "/b",
      component: componentB,
      children: [
        {
          path: "c2d",

          // 使用 components 載入多個元件
          components: {
            //填入 name 的值，並引入對應的子元件檔案
            left: () => import("../views/ComponentC.vue"),
            right: () => import("../views/ComponentD.vue"),
          },
        },
        {
          path: "d2c",
          components: {
            left: () => import("../views/ComponentD.vue"),
            right: () => import("../views/ComponentC.vue"),
          },
        },
      ],
    },
  ],
});
```

### 應用三、動態路由

可以產生多個頁面，常用於建立 100 個商品的詳細頁面

1. 在路由表使用 `:` 加入動態路由，後方可以是自定義名稱

```javascript=
// 元件檔案
const router = VueRouter.creteRouter({
  history: VueRouter.createWebHashHistory(),
  router: [
    {
      // 加入 : 轉換為動態路由
      path: "/product/:id",
      component: componentProduct,
    },
  ],
});
```

2. 補充：元件裡面也可取出動態路由的參數值

```javascript=
export default {
  created() {
    // 取得目前網址路徑的特定參數
    console.log(this.$route.params.id);
  },
};
```

3. 使用 props

```javascript=
const router = VueRouter.creteRouter({
  history: VueRouter.createWebHashHistory(),
  router: [
    {
      // 加入 : 轉換為動態路由
      path: "/product/:id",
      component: componentProduct,

      // 透過 props 即時取得目前動態路由的內容
      props: (route) => {
        // console.log("route", route);
        return {
          id: route.params.id,
        };
      },
    },
  ],
});
```

```javascript=
// 元件檔案
export default {

  // 這裡的 id 會取得在路由表裡定義好的 id
  props: ['id'],
  created() {
    console.log(this.id);
  },
};
```

### 應用四：預設路徑與重新導向

若沒有設定 404 頁面，若進入錯誤路徑會出現空白頁面，建議在路由表中設定重導向、或是出現 404 頁面。

- 404 頁面

```javascript=
{
  // 任意路徑下若無此路由就出現 404 頁面
  path: '/:pathMatch(.*)*',
  component: () => import('../views/NotFound.vue'),
}
```

- 重新導向

```javascript=
{
  // 在 newPage 路徑下若無此路由就重導向到首頁
  path: '/newPage/:pathMatch(.*)*',
  redirect: {
    name: 'Home'
  }
}
```

### 其他路由表選項

- `scrollBehavior`：可以每次切換頁面時，都要觸發回到頁面頂部
- `linkActiveClass`：為目前所在的路由加上指定樣式

```javascript
const router = createRouter({
  history: createWebHashHistory(),
  routes,
  linkActiveClass: "active",

  // 切換頁面時會觸發
  scrollBehavior(to, from, savePosition) {
    console.log(to, from, savePosition);

    // to 和 from 都是路由地址，savePosition 預設是 null
    // 當路徑符合 product 時，就滾動到頁面最上方
    if (to.fullPath.match("product")) {
      return {
        top: 0,
      };
    }
    // 其他路徑就維持預設
    return {};
  },
});
```


# `$route` 與 `$router` 的差別

- `$route`：當前路由的資訊
- `$router`：該路由可以使用的方法

```javascript=
console.log(this.$route)
console.log(this.$router)
```

## $route 常用屬性

- `fullPath`：完整網址路徑
- `params`：傳入的參數
- `query`：搜尋的內容

## $router

包含很多方法，通常會在元件中建立函式來使用這些方法

### 範例 1：切換頁面

- push：會包含歷史紀錄，可以回到上一頁
  - 代入路由
```javascript=
this.$router.push('/home')
```

  - 帶入 name 屬性
  ```javascript=
  this.$router.push({ name: '首頁'})
  ```
  
- replace：沒有歷史紀錄

```javascript=
this.$router.replace('/home')
```

### 範例 2：操作歷史紀錄

- go

```javascript=
// 回到上一頁
this.$router.go(-1)

// 到下一頁
this.$router.go(1)
```

# 參考文件

- [官方文件](https://next.router.vuejs.org/)
- [中文版官方文件](https://next.router.vuejs.org/zh/index.html)

> 注意：Vue 3 搭配的 Router 版本在網址中會有 "next" 的字樣，而 [router.vuejs.org](http://router.vuejs.org/) 是 Vue 2 版本的路由
