---
title: TypeScript 學習的開始
date: 2024-09-28 14:32:40
tags: [111]
categories: [111]
excerpt: 111
index_img: "/img/restart.jpg"
banner_img: "/img/restart.jpg"
---

## 前言

先前就意會到該要來學習 TypeScript，畢竟以前端的大環境來說，不論在主流的三大框架、開發工具、企業需求等，都能看到 TypeScript 越來越普遍的趨勢。而以企業需求來說，現在 Junior 的職缺技能要求，也會看到 TypeScript 或者會列在加分項目。
（~~不過我這學習真的是拖延了許久~~）

## TypeScript 的優勢

簡單來說，TypeScript 是 JavaScript 的強化版，能讓 JavaScript 的語法自由補上嚴謹。

1. 靜態型別檢查、減少錯誤：使開發者能在編譯時，就先看到錯誤並做調整，不會在程式執行時才發生問題，提高程式碼的可靠性跟可維護性。（現在以 VSCode 來說，甚至還不用編譯時，編輯器就幫我們點出錯誤）
2. 兼容 JavaScript：目前寫的 JavaScript 程式碼可直接無痛遷移到 TypeScript，然後再逐步調整。簡單來說我們可以在 .ts 撰寫一般的 JavaScript。
3. 支援 ECMAScript 新功能：開發者能直接使用 JavaScript 的最新特性，而不用等到瀏覽器支援。
4. 有益於團隊配合：TypeScript 的強型別，能為團隊配合帶來嚴謹跟規則，像是明確的型別定義有助於團隊成員更快理解彼此寫的程式碼。

## 安裝 TypeScript 與小試身手

### 安裝

安裝 TypeScript 之前需要先下載 Node。

可以將 TypeScript 只安裝在專案，也可以安裝在全局。差異在於 tsc 指令只能在某個專案內的終端機使用，或者在任何位置的終端機都能使用。

安裝在專案：

```js
npm install typescript --save-dev
```

安裝在全局：

```bash
npm install typescript -g
```

### 編譯

因為瀏覽器無法直接讀取 TypeScript 檔案（.ts），所以我們需要把 TypeScript 編譯為 JavaScript 檔案（.js）。我們可以透過`tsc`語法來進行編譯，例如在終端機輸入：

```bash
tsc index.ts
```

透過以上指令，我們就能將 `index.ts` 編譯成 `index.js`，而這個檔案就能被瀏覽器讀取哩。

### 小試身手

我們現在有一支 hello.ts 檔案，內容為：

```js
function greet(person: string) {
	return `Hello, ${person}!`;
}

let user = "Billy";
console.log(greet(user));
```

![hello.ts編譯為hello.js](img/typescript-start-1.png)

---

初始化 TypeScript Config File
會產出 tsconfig.json

```
npx tsc --init
```

腳手架、模板則不用產。

---
