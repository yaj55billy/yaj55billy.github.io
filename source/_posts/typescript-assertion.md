---
title: TypeScript 型別斷言
date: 2024-11-06 17:45:26
tags: [JavaScript, TypeScript]
categories: [前端 TypeScript 系列]
excerpt: TypeScript 型別斷言
index_img: "/img/typescript.jpg"
banner_img: "/img/article-banner.jpg"
---

## 前言

在前段時間撰寫的「[TypeScript 型別註記與型別推論](https://yaj55billy.github.io/post/typescript-gradual-typing.html#)」，因為篇幅關係，所以沒有討論到型別註記中包含的**型別斷言（Type Assertion）**，而這篇文章要來整理補上這個觀念。

## 型別斷言的寫法

TypeScript 提供兩種型別斷言的語法，而語法上看到關鍵字 `as` 或者 `<Type>(…)` 這樣的格式就是斷言的寫法。如下範例：

```tsx
let someValue = "Hello, world!";

// 型別斷言第一種寫法
let strLength = (someValue as string).length;

// 型別斷言第二種寫法
let strLength2 = (<string>someValue).length;
```

_> 在 React 的 JSX 語法中，如果有使用到 TypeScript，則只能使用 `as` 這個寫法。因為在 JSX 中 `<>` 會被視作標籤。_

### 註記與斷言的不同之處

型別註記與型別斷言這兩者的本質相似，但卻是完全不同的概念。

- 型別註記：在宣告變數或函式參數時，明確告訴 TypeScript 編輯器**該變數應該是什麼型別**。讓編輯器「**依循」**這樣的型別規則，並會根據此規則幫我們檢查型別。
- 型別推斷：依照我們自身的判斷，明確告訴 TypeScript 編譯器**某表達式之結果應該是什麼型別**。也就是我們斷定的型別，去「**覆蓋**」TypeScript 編譯器所推斷的型別。

## 型別斷言的使用情境

當程式沒辦法推斷出某表達式的運算結果之型別時，我們就可以使用型別斷言。以下舉例幾個情境：

### 未知結果的函式

假設我們有個函式`fetchDataFromLibrary`，因為它可能根據某個外部資源獲取資料，所以返回的型別不確定；不過我們可能從套件中的文件得知它會回傳某種特定型別（下方範例中是斷言為 `string`）。

```tsx
function fetchDataFromLibrary() {
	// ...
	// 假設這個函式從某個外部資源獲取資料
	return "Library Data";
}

// 使用型別斷言來告訴 TypeScript 返回值是 `string`
const dataString = fetchDataFromLibrary() as string;

// 另一種斷言寫法
const dataStringAlt = <string>fetchDataFromLibrary();
```

---

Photo by <a href="https://unsplash.com/@safarslife?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Safar Safarov</a> on <a href="https://unsplash.com/photos/turned-on-gray-laptop-computer-MSN8TFhJ0is?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
