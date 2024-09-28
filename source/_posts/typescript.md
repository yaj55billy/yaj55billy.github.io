---
title: TypeScript 學習的開始
date: 2024-09-28 14:32:40
tags:
categories:
excerpt:
index_img:
banner_img:
---

## 前言 murmur

先前就意會到該要來學習 TypeScript，畢竟以前端的大環境來說，不論在主流的三大框架、開發工具、企業需求等，都能看到 TypeScript 越來越普遍的趨勢。而以企業需求來說，現在 Junior 的職缺技能要求，也會看到 TypeScript 或者會列在加分項目。
（~~不過我這學習真的是拖延了許久~~）

## TypeScript 的優勢

簡單來說，TypeScript 是 JavaScript 的強化版，能讓 JavaScript 的語法自由補上嚴謹。

1. 靜態型別檢查、減少錯誤：使開發者能在編譯時，就先看到錯誤並做調整，不會在程式執行時才發生問題，提高程式碼的可靠性跟可維護性。（現在以 VSCode 來說，甚至還不用編譯時，編輯器就幫我們點出錯誤）
2. 兼容 JavaScript：目前寫的 JavaScript 程式碼可直接無痛遷移到 TypeScript，然後再逐步調整。簡單來說我們可以在 .ts 撰寫一般的 JavaScript。
3. 支援 ECMAScript 新功能：開發者能直接使用 JavaScript 的最新特性，而不用等到瀏覽器支援。
4. 有益於團隊配合：TypeScript 的強型別，能為團隊配合帶來嚴謹跟規則，像是明確的型別定義有助於團隊成員更快理解彼此寫的程式碼。

## 安裝與...

專案中使用

```
npm install typescript --save-dev
```

安裝在全局，整台電腦都能使用 typescript 編譯（語法）

```
npm install typescript -g
```

編譯：
ts 無法直接被瀏覽器讀取，所以是需要被編譯的
需要被編譯

```
tsc index.ts // 要編譯的檔案名稱
```

初始化 TypeScript Config File
會產出 tsconfig.json

```
npx tsc --init
```

腳手架、模板則不用產。

---

## TypeScript 變數

JavaScript 變數的特性

變數可以賦予任何的資料型態，在程式不複雜時，這樣的彈性並不會帶來什麼問題；
不過如果程式開始變複雜時，就容易有些意外的 Bug

```js
let x = 3;
x = true;
x = "abc";
```

---

- 數字陣列 `number[]`、`Array<number>`
- 字串陣列 `string[]`、`Array<string>`
- 布林值陣列 `boolean[]`、`Array<boolean>`
- 任意資料型態陣列 `any[]`、`Array<any>` （盡量不要使用，畢竟都已經在寫 ts）

基本語法：
let 變數名稱:資料型態 = 資料

```ts
let x: number = 3;
x = true; // compile time error
let y: string[] = [3, "a", "b"]; // compile time error

let s: string;
s = "我是字串";
console.log(s); // 不會錯誤
s = true;
console.log(s); // 這樣就會產生錯誤
```

```ts
let grades: number[] = [60, 70, 80];
console.log(grades);
// ...
grades.push("hello"); // 編譯會產生錯誤 Argument of type 'string' is not assignable to parameter of type 'number'.

let a: Array<boolean>;
a = [true, false];
console.log(a); // 這樣就不會錯誤
```

---
