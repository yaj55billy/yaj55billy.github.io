---
title: Pinia 筆記
date: 2023-08-12 
tags:
  - 前端
  - pinia
categories:
  - 前端
description: xxx
cover: https://i.imgur.com/BkwBMOG.png
---

## Pinia 

## 前言
一段時間沒接觸前端領域，能很明顯感受到前端的變化如此之快。以 Vue 來說，透過官網提供的安裝 `npm init vue@latest`，也已經預設 vite 環境，以及把原先的 Vuex 改為 Pinia。

目前正在複習 Vue ，這邊也把 Pinia 做個筆記，關於 Pinia 的特點：
- 跟 Vuex 很像，都是作為狀態管理的工作
- 沒有 Mutation （後來才知道有些開發者不喜歡這個步驟）
- 簡潔定義
- 可以寫 Option api ，也能寫 Composition api
- 支援 Typescript 
- ~~可愛的官方吉祥物~~

----

## 撰寫

先以 store/counter.js 作為範例，我們需要先從 pinia 引入 defineStore 這個功能。

如下範例，上方是用 composition api 的寫法，而下方是原本的 option 寫法 (Vuex 是這樣寫的)，兩種方式的選擇可依照自己所偏好的。在下方的範例中，將會以 composition api 的方式作筆記。

對照：
| Option   | Composition | 
| -------- | ----------- | 
| state    | ref         | 
| getters  | computed    | 
| action   | function    |

```js
import { ref, computed } from 'vue';
import { defineStore } from 'pinia';

// composition 
export const useCounterStore = defineStore('counter', () => {
	const count = ref(0);
	const doubleCount = computed(() => count.value * 2);
	function increment() {
		count.value++
	}
	return { count, doubleCount, increment }
})
  
// option
export const useCounterStore = defineStore({
	id: "counter",
	state: () => ({
		counter: 0,
	}),
	getters: {
		doubleCount: (state) => state.counter * 2,
	},
	action: {
		addCount() {
			this.counter ++;
		},
	}
});
```

在元件的引用中，先透過路徑 import useCounterStore 這個功能，然後再放到 store 這個變數。這邊因為使用了 script setup 這個語法糖，所以不用使用 return 就可在樣板中使用。

（這支範例程式是 .vue 檔案，不過因為程式碼無法產生高亮反應，所以先以 html 作為替代）

```html
<script setup> 
	import { ref } from 'vue'; 
	import { useCounterStore } from "@/stores/counter.js"; 
	const store = useCounterStore(); 
	const triggerStoreIncrement = () => { 
		store.increment(); 
	}
</script> 
<template> 
	<h1>Pinia</h1> 
	pinia資料：{{ store.count }} 
	<button type="button" @click="triggerStoreIncrement">
		觸發 pinia increment
	</button> 
</template> 

```

### api 的處理

先前用 vuex 時，如果要打個 api，步驟是：
1. 元件做 dispatch 
2. 在 action 打 api，並且 commit
3. 透過 commit 去觸發 mutation，然後改資料 (state)

不過現在 pinia + composition api，變得比較簡潔（自由？），如下方程式碼所示：

```js products.js
import { ref } from 'vue';
import axios from "axios";
import { defineStore } from 'pinia';

export const useProductsStore = defineStore('product', () => {
	const products = ref([]);
	const fetchApi = async() => {
		try {
			const res = await axios.get('https://fakestoreapi.com/products');
			products.value = res.data;
		} catch (error) {
			// error
		}
	}
	return { products, fetchApi }
})

```

```html products.vue
<script setup>
	import { useProductsStore } from "@/stores/products.js";
	const store = useProductsStore();
	const triggerStoreFetchApi = () => {
		store.fetchApi();
	}
</script>

<template>
	<h1>產品頁</h1>
	<div>{{ store.products }}</div>
	<button type="button" @click="triggerStoreFetchApi">Get API</button>
</template>

```

### 關於解構的可能問題

在元件中透過解構，我們可以達到更乾淨的 template，


當我們從元件去接 pinia 的資料時，只要是 ref 相關，


```html
<script setup> 
	import { ref } from 'vue'; 
	import { useCounterStore } from "@/stores/counter.js"; 
	const store = useCounterStore(); 
  const { count } = storeToRefs(store); 
	const triggerStoreIncrement = () => { 
		store.increment(); 
	}
</script> 
<template> 
	<h1>Pinia</h1> 
	pinia資料：{{ store.count }} 
	<button type="button" @click="triggerStoreIncrement">
		觸發 pinia increment
	</button> 
</template> 

```

這邊先簡單紀錄：
- 在 pinia 的函式可直接解構使用
- 在 pinia 的資料不能直接解構，因為有包一層 proxy 物件，如果解構會無法有響應式。
	- storeToRefs() 

### pinia store 不同檔案的資料傳遞



```js
import { ref } from 'vue';
import axios from "axios";
import { defineStore, storeToRefs } from 'pinia';

import { useCounterStore } from "./counter.js"; // 引用另一支 pinia 檔案

export const useProductsStore = defineStore('product', () => {
	const counterStore = useCounterStore(); // 使用時命名需做個區隔
	const { count } = storeToRefs(counterStore); // 解構
	// ... 略
}

// ... 略

```

ref computed 會回傳 ref 物件，所以解構要經過處理
reactive 跟 function 則不用，直接從 store 引出來即可



```js counter.js
import { ref, computed } from 'vue';
import { defineStore } from 'pinia';

export const useCounterStore = defineStore('counter', () => {
	const count = ref(0);
	const doubleCount = computed(() => count.value * 2);
	function increment() {
		count.value++
	}
	return { count, doubleCount, increment }
})
```




