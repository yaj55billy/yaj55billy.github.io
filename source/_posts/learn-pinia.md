---
title: Pinia 筆記
date: 2023-08-14
excerpt: 目前正在複習 Vue ，這邊也把 Pinia 做個筆記。Pinia 跟先前的 Vuex 都是拿來做狀態管理的...
index_img: https://i.imgur.com/BkwBMOG.png
banner_img: https://i.imgur.com/BkwBMOG.png
tags: [pinia]
categories: [前端]
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

---

## 筆記

### 基本使用

先以 store/counter.js 作為範例，我們需要先從 pinia 引入 defineStore 這個功能。

如下範例，上方是用 composition api 的寫法，而下方是原本的 option 寫法 (Vuex 是這樣寫的)，兩種方式的選擇可依照自己所偏好的。在下方的範例中，將會以 composition api 的方式作筆記。

對照：
| Option | Composition |
| -------- | ----------- |
| state | ref |
| getters | computed |
| action | function |

```js
import { ref, computed } from "vue";
import { defineStore } from "pinia";

// composition
export const useCounterStore = defineStore("counter", () => {
	const count = ref(0);
	const doubleCount = computed(() => count.value * 2);
	function increment() {
		count.value++;
	}
	return { count, doubleCount, increment };
});

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
			this.counter++;
		},
	},
});
```

在元件的引用中，先透過路徑 import useCounterStore 這個功能，然後再放到 store 這個變數。這邊因為使用了 script setup 這個語法糖，所以不用使用 return 就可在樣板中使用。

（這支範例程式是 .vue 檔案，不過因為程式碼無法產生高亮反應，所以以 html 作為替代）

```html
<script setup>
	import { ref } from "vue";
	import { useCounterStore } from "@/stores/counter.js";
	const store = useCounterStore();
	const triggerStoreIncrement = () => {
		store.increment();
	};
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

```js
// products.js
import { ref } from "vue";
import axios from "axios";
import { defineStore } from "pinia";

export const useProductsStore = defineStore("product", () => {
	const products = ref([]);
	const fetchApi = async () => {
		try {
			const res = await axios.get("https://fakestoreapi.com/products");
			products.value = res.data;
		} catch (error) {
			// error
		}
	};
	return { products, fetchApi };
});
```

```html
<!-- products.vue -->
<script setup>
	import { useProductsStore } from "@/stores/products.js";
	const store = useProductsStore();
	const triggerStoreFetchApi = () => {
		store.fetchApi();
	};
</script>

<template>
	<h1>產品頁</h1>
	<div>{{ store.products }}</div>
	<button type="button" @click="triggerStoreFetchApi">Get API</button>
</template>
```

### 關於解構的可能問題

當我們從元件去接 pinia 資料或方法時，可以透過解構達到更乾淨的 template，讓我們不用在 template 寫 `store.xxxxxxx`；不過如果是跟 ref 相關的資料 (ref、computed) ，解構會造成資料失去響應式，這時就可以用 pinia 提供的 `storeToRefs()`，來達成解構 ref 資料但不失去響應式。

解構方面的問題，主要是對於 ref 相關資料；如果是 reactive 或函式就可以直接解構使用。

```html
<script setup>
	import { ref } from "vue";
	import { useCounterStore } from "@/stores/counter.js";
	const store = useCounterStore();

	const { count } = storeToRefs(store);
	const { increment } = store; // 函式直接解構使用
</script>

<template>
	<h1>Pinia</h1>
	pinia資料：{{ count }}
	<button type="button" @click="increment">觸發 increment</button>
</template>
```

---
