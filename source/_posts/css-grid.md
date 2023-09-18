---
title: CSS GRID
date: 
tags:
  - 前端
  - CSS
  - GRID
categories:
  - 前端
description: 
cover: https://i.imgur.com/BkwBMOG.png
hidden: true
---

## CSS GRID 

## 前言

前言

----

## 筆記

```html
.container
  .item1
  .item2
  .item3

```

```css

.container {
  /* 起手 */
  display: grid;
  /* 設定格線的列（橫） */
  grid-template-rows: 100px 100px auto 100px;
  /* 設定格線的欄（直） */
  grid-template-columns: 200px 100px 20% 100px;
  grid-template-areas: "item1 item1 item3 item3"
  "item1 item1 item3 item3"
  "item1 item1 item3 item3"
  "item2 item2 item2 item2";
}

.item1{
  /* 定義區域名稱 */
  grid-area: item1; 
}
.item2{
  grid-area: item2;
}
.item3{
  grid-area: item3;
}

```

fr 是幾分之幾的概念


欄位數如果太多
```css
.xxx {
  grid-template-areas: "item1 item1 item1 item1 item2 item2 item2 item3 ..."
  /* ...  */
}


.container {
  width: 500px;
  min-height: 500px;
  margin: 0 auto;
  border: solid 2px #000;
  display: grid;
  grid-template-rows: 25% 25% 25% 25%;
  grid-template-columns: 25% 25% 25% 25%;
  grid-gap: 12px;
}
.item1 {
  grid-area: item1;
  grid-row: 1/2;
  grid-column: 1/2;
  background-color: #ccc;
}
.item2 {
  grid-area: item2;
  grid-row: 2/3;
  grid-column: 2/3;
  background-color: #3ca;
}
.item3 {
  grid-area: item3;
  grid-row: 3/4;
  grid-column: 3/4;
  background-color: #fef;
}
  
```

不指定幾欄跟幾列，但可以自動產出：
概念類似，會去抓子層最大的數字，以這個數字來做分欄或分列的依據產出
```css
.container {
  width: 500px;
  min-height: 500px;
  margin: 0 auto;
  border: solid 2px #000;
  display: grid;
/*   grid-template-rows: 25% 25% 25% 25%;
  grid-template-columns: 25% 25% 25% 25%; */
  grid-auto-rows: 1fr;
  grid-auto-column: 1fr;
  grid-gap: 12px;
}
.item1 {
  grid-area: item1;
  grid-row: 1/2;
  grid-column: 1/2;
  background-color: #ccc;
}
.item2 {
  grid-area: item2;
  grid-row: 2/3;
  grid-column: 2/3;
  background-color: #3ca;
}
.item3 {
  grid-area: item3;
  grid-row: 3/4;
  grid-column: 3/4;
  background-color: #fef;
}
```

---

Flex 的對齊在 Grid 也可以使用

---

minmax()

---

----

## 結尾









