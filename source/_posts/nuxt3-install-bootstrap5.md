---
title: 在 Nuxt3 使用 Bootstrap5
date: 2023-08-22
tags:
  - 前端
  - Nuxt3
  - Bootstrap5
categories:
  - 前端
description: 記錄在 Nuxt3 如何安裝並引用 Bootstrap5
cover: https://i.imgur.com/BkwBMOG.png
---

## 前言

近期正在練習把 Vue2 的舊專案翻成 Nuxt3，因為過去這個專案是使用 Bootstrap4，想說就順道升上 Bootstrap5。

不過 Nuxt3 跟 Bootstrap5 並沒有一個整合好的套件，
所以引用上就會多一些設置，這邊也把這些設置記錄下來。

本來是有考慮乾脆把專案翻到 tailwind，畢竟在套件上是有整合的，不過後來先打退堂鼓，還是專心在 Nuxt3 的熟悉
https://nuxt.com/modules/tailwindcss

## 最簡單的安裝方式

直接在 nuxt.config.ts 設置 cdn 路徑，
需要手動做版本，並且不能客制 sass

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

## 另一安裝配置

在 nuxt3 專案的終端機中，輸入以下：
有一些 BS5 效果如 popper、tooltip 會需要 Popper.js 才能作用
```bash
npm install bootstrap @popperjs/core
```

css 設置：
我們可以直接引入 node_modules 的 scss，當然這樣也就無法做客製，
另一種是在 /assets/styles 創建一支 main.scss
```ts
export default defineNuxtConfig({
  css: [
    // 'bootstrap/scss/bootstrap.scss',
    '@/assets/styles/main.scss',
  ],
});

```

這樣我們就可以使用 bootstrap 客制，也能用上自己預計想要的 scss
@import "./variables"; 這支是從 node_module 複秩過來
 
```scss 
@import "bootstrap/scss/functions";
@import "./variables";
@import "bootstrap/scss/bootstrap";

@import "./common";
@import "./page/prod";
@import "./page/home";
// ... 略
```

JS 的處理

在 plugins 創建 useBootstrap.client.js

這個 plugins 還可以做到 vueApp 或者是 directive 的設置

```js 
import bootstrap from "bootstrap/dist/js/bootstrap.bundle";

export default defineNuxtPlugin((nuxtApp) => {
	nuxtApp.provide("bootstrap", bootstrap);
});
```

如何使用：
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
	getProducts();

	productModalHandle = new $bootstrap.Modal(productModal.value, {}); // 記得綁上 ref
	delProductModalHandle = new $bootstrap.Modal(delProductModal.value, {});
});
</script>


```

## 如果有遇到

如果有遇到 scss abs 函式錯誤或警告，則可以把 scss 降低版本


參考：
https://stackoverflow.com/questions/71795143/how-to-use-bootstrap5-with-vite-and-nuxt3


