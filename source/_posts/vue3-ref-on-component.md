---
title: Vue3 取得子組件中的 ref(DOM)
date: 2023-09-22
tags:
  - 前端 
  - Vue3
categories:
  - 前端
description: 筆記在 Vue3 中，如何取得子組件中的 ref
cover: https://i.imgur.com/Zwb2dxR.jpg
hidden: true
---

<!-- Photo by <a href="https://unsplash.com/@lautaroandreani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Lautaro Andreani</a> on <a href="https://unsplash.com/photos/xkBaqlcqeb4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a> -->

## 前言

先前使用 Vue3 時較少寫到動態部分，所以對於 ref 取得 dom 比較沒有那麼熟悉，這次實作 Header NAV 點擊時，想滑動到指定位置，遇到了一些取 dom 的問題，所以就來研究一下。

下方程式範例，實際為 .vue 檔案，不過目前的 theme 似乎不支援，所以先以 html 取代

----

## 基礎使用（概念）

因為在 Composition API 已經沒有 this（this.$ref），所以當我們要存取元件內的 DOM 時，需要用下方的寫法，這邊直接使用 `<script setup>` 語法糖方式作為範例

- 在程式中，我們需要宣告一個 ref 來存放樣板元素
  - 必須與樣板的 ref 同名

```js

<script setup>

import { ref, onMounted } from 'vue';

const input = ref(null); // input 命名與 <input ref="input" /> 配合

onMounted(() => {
  // 當元件掛載完成，我們就可以透過 input.value 取得實際 DOM 元素，或者進行一些操作
  console.log(input.value);
  input.value.focus();
});

// return {} 因為使用 script setup 所以不需要 return

</script>

<template>
  <input ref="input" />
</template>

```

## 如何取子組件的 ref 

針對取得子組件的 ref，這邊會提到一般組件寫法跟 script setup 語法糖的不同之處。
- 在一般寫法中，父組件對子組件的每一個屬性和方法都完全有訪問權，所以像是 ref 來說，我們能透過 $refs 去取東西（待驗證）

- 而在使用 script setup 的組件中，組件默認是私有的，只給自身組件的 template 使用，不會暴露到外頭。所以父組件無法訪問子組件的任何東西（ref 變量）。


### 一般組件寫法

- 定義好 skill ref

父層
```js index.vue
<script>
import { ref, onMounted } from "vue";
import Skill from "@/components/Skill.vue";

export default {
  components: {
    Skill,
  },
  setup() {
    const skill = ref(null);

    const scrollToTarget = () => {
      // .. 略
    };
    onMounted(() => {
      console.log(skill.value.$refs.skillChild);
      console.log(skill.value);
    });
    return {
      skill,
      scrollToTarget,
    };
  },
};
</script>

<template>
  <div class="index">
    <button class="btn h1" @click="scrollToTarget">到 skill 區塊</button>
    // ... 其他區塊略
    <Skill ref="skill" />
    // ... 略
  </div>
</template>

```

子層
```js skill.vue
<script>
export default {
  setup() {
    const childFunc = () => {
      console.log("childFunc");
    };
    return {
      childFunc,
    };
  },
};
</script>

<template>
  <section ref="skillChild" class="section">
    <div class="container">
      <div class="section__header">
        <h2 class="section__title">skill</h2>
      </div>
      // ... 略
    </div>
  </section>
</template>

```


### script setup 寫法
 

```js index.vue

<script setup>
import { ref, onMounted } from "vue";
import Skill from "@/components/Skill.vue";

const hero = ref(null);
const about = ref(null);
const skill = ref(null);

const scrollToTarget = () => {
  console.log(1);
  const targetComponent = skill.value.skillComponent;
  if (skill.value) {
    targetComponent.scrollIntoView({
      behavior: "smooth",
      block: "start",
    });
  }
};
onMounted(() => {
  console.log(skill.value.skillComponent);
});
</script>

<template>
	<div class="index">
		<button class="btn h1" @click="scrollToTarget">到 about 按鈕</button>
		<Header />
		<Hero ref="hero" />
		<About ref="about" />
		<Skill ref="skill" />
		<Experience />
		<Project />
		<Footer />
	</div>
</template>

```

子層

```js

<script setup>
import { ref } from "vue";

const a = 1;
const b = ref(2);
const skillComponent = ref(null);

const childFunc = () => {
	console.log("childFunc");
};

defineExpose({
	a,
	b,
	skillComponent,
});
</script>

<template>
	<section ref="skillComponent" class="section skill">
		<div class="container">
			<div class="section__header">
				<h2 class="section__title">skill</h2>
			</div>
		</div>
	</section>
</template>

```



如果要调用子组件的数据，需要先在子组件显示的暴露出来，才能够正确的拿到，这个操作，就是由 defineExpose 来完成。

Vue 3.2 版之後，有提供 defineExpose 這個方式，








----

## 結尾
- 最近在練習盡量看文件，不過總覺得有點..因為有些文件並沒有很好閱讀
- 推薦 Alex 大大的影片，因為有些文件的內容，即使翻譯成簡中，還是有些意思看不太懂
- 最近發現學習支援越來越多，看書是一個很好的選擇，像是站在巨人肩膀

參考資料：
[官方文件](https://cn.vuejs.org/guide/essentials/template-refs.html)


https://xie.infoq.cn/article/97c3fca6faf21bc73dd5e65ed



通过子组件emit传递ref
https://juejin.cn/post/7154704730789249038


provide inject 傳遞
https://penueling.com/%E7%B7%9A%E4%B8%8A%E5%AD%B8%E7%BF%92/vue3-%E5%9C%A8%E7%88%B6%E5%B1%A4%E5%8F%96%E5%BE%97%E5%AD%90%E7%B5%84%E4%BB%B6%E7%9A%84-ref-dom%E5%B1%AC%E6%80%A7/
