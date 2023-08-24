---
title: 在 Nuxt3 使用 Bootstrap5
date: 2023-08-24
tags:
  - 前端
  - Nuxt3
  - Bootstrap5
categories:
  - 前端
description: 記錄在 Nuxt3 如何安裝並引用 Bootstrap5
cover: https://i.imgur.com/OpmAfpH.jpg
---

## 前言

近期正在練習把 Vue2 的舊專案翻成 Nuxt3，因為過去這個專案是使用 Bootstrap4，想說就順道升上 Bootstrap5。不過 Nuxt3 跟 Bootstrap5 並沒有一個整合好的套件（例如：BootstrapVue），所以在引用上就會多一些設置。

（一開始在考慮 Nuxt3 的套件整合上，本來是有考慮翻到 Tailwind，不過考慮到重切時間跟專注度，決定還是先專心熟悉 Nuxt3）

----

## 設置部分

### CDN 配置

這邊先提一個最簡易的配置，如果只是做個 Demo，沒有要使用到客製化，那麽就可以在 nuxt.config.ts 直接引入 CDN 使用。

```ts nuxt.config.ts
import { defineNuxtConfig } from 'nuxt'
export default defineNuxtConfig({
  app: {
    head: {
      link: [
        { rel: 'stylesheet', href: 'https://cdn.jsdelivr.net/npm/bootstrap@5.1.1/dist/css/bootstrap.min.css', integrity: 'sha384-F3w7mX95PdgyTmZZMECAngseQB83DfGTowi0iMjiWaeVhAn4FJkqJByhZMI3AhiU', crossorigin: 'anonymous' }
      ],
      script: [
        { src: 'https://cdn.jsdelivr.net/npm/bootstrap@5.1.1/dist/js/bootstrap.bundle.min.js', integrity: 'sha384-/bQdsTh/da6pkI1MST/rWKFNjaCP5gBSY4sEBT38Q/9RBh9AH40zEOg7Hlq2THRZ', crossorigin: 'anonymous' }
      ]
    }
  }
})
```

### 安裝

先在專案的終端機中，輸入以下 `npm install bootstrap @popperjs/core`。Popper.js 是跟一些 Bootstrap5 效果如 popper、tooltip 有關。

### css 設置

我們可以在 nuxt.config.ts 的設置中，直接引入 bootstrap.scss，當然這樣就無法去客製；

這邊是在 assets 創建一個 main.scss，然後 `@import "./variables";` 這個部分是從 node_module bootstrap 複製過來的，這樣就可以去客製一些 bootstrap 變數（例如：顏色、距離等等）。然後也能帶上自己要寫的 scss。

```ts nuxt.config.ts
import { defineNuxtConfig } from 'nuxt'
export default defineNuxtConfig({
  css: [
    // 'bootstrap/scss/bootstrap.scss',
    '@/assets/styles/main.scss',
  ],
});

```
 
```scss main.scss
@import "bootstrap/scss/functions";
@import "./variables";
@import "bootstrap/scss/bootstrap";

@import "./common";
@import "./page/prod";
@import "./page/home";
// ... 略
```

### JS 的設置

在 Nuxt 專案的根目錄中，創建 plugins 的資料夾，並且新建一支 JS 檔案，這邊範例是用 useBootstrap.client.js 作為命名。

接下來把 node_module 中的 bootstrap.bundle.js 給引用進來，接著 `nuxtApp.provide("bootstrap", bootstrap)` 將 bootstrap.bundle.js 的功能引用到 Nuxt 中。

```js useBootstrap.client.js
import bootstrap from "bootstrap/dist/js/bootstrap.bundle";

export default defineNuxtPlugin((nuxtApp) => {
	nuxtApp.provide("bootstrap", bootstrap);
});
```

接著我們可以透過 `const { $bootstrap } = useNuxtApp()` 來使用 bootstrap js，記得要加上 $ 符號。
以下範例示意，當去打 api 拿回資料後，才去觸發 Modal：

```html
<script setup>

// bootstrap js
const { $bootstrap } = useNuxtApp();

// ... 其他程式略

$fetch(api, {
  method: "GET",
  headers: headers,
})
  .then((res) => {
    const { data } = res;
    // ... 略
    productModalHandle.show();
  })
	
onMounted(() => {
  productModalHandle = new $bootstrap.Modal(productModal.value, {}); // 記得綁上 ref
  delProductModalHandle = new $bootstrap.Modal(delProductModal.value, {});
});
</script>

```

----

## 結尾

以上是參考這篇 [stackoverflow](https://stackoverflow.com/questions/71795143/how-to-use-bootstrap5-with-vite-and-nuxt3)，並做個延伸。

如果有遇到 sass 的 abs 警告，則可以試著把 sass 的版本降低。




