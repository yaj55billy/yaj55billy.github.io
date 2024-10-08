---
title: TypeScript 學習的開始
date: 2024-10-03 16:26:40
tags: [JavaScript, TypeScript 系列]
categories: [前端]
excerpt: 以前端的大環境來說，不論在主流的三大框架、開發工具、企業需求等，都能看到 TypeScript 越來越普遍的趨勢。
index_img: "/img/typescript.jpg"
# banner_img: "/img/typescript.jpg"
---

## 前言

先前就意會到該要來學習 TypeScript，畢竟以前端的大環境來說，不論在主流的三大框架、開發工具、企業需求等，都能看到 TypeScript 越來越普遍的趨勢。而以企業需求來說，現在 Junior 的職缺技能要求，也會看到 TypeScript 或者會列在加分項目。
（~~不過我這學習真的是拖延了許久~~）

## TypeScript 的優勢

簡單來說，TypeScript 是 JavaScript 的強化版，能在相對自由的 JavaScript 語法補充「型別」，增添開發的嚴謹。

1. 靜態型別檢查、減少錯誤：使開發者能在編譯時，就先看到錯誤並做調整，不會在程式執行時才發生問題，提高程式碼的可靠性跟可維護性。（現在以 VSCode 來說，甚至還不用編譯時，編輯器就幫我們點出錯誤）
2. 兼容 JavaScript：目前寫的 JavaScript 程式碼可直接無痛遷移到 TypeScript，然後再逐步調整。簡單來說我們可以在 .ts 撰寫一般的 JavaScript。
3. 支援 ECMAScript 新功能：開發者能直接使用 JavaScript 的最新特性，而不用等到瀏覽器支援。
4. 有益於團隊配合：型別定義可以更清楚程式碼（變數、函式）的用途，有助於團隊成員更快理解彼此撰寫的程式碼。

## 安裝 TypeScript 與小試身手

### 安裝

安裝 TypeScript 之前需要先下載 Node。

可以將 TypeScript 只安裝在專案，也可以安裝在全局。差異在於 tsc 指令只能在某個專案內的終端機使用，或者在任何位置的終端機都能使用。

安裝在專案：

```bash
npm install typescript --save-dev
```

安裝在全局：

```bash
npm install typescript -g
```

### 編譯

因為瀏覽器無法直接讀取 TypeScript 檔案（.ts），所以我們需要把 TypeScript 編譯為 JavaScript 檔案（.js）。我們可以透過 `tsc` 語法來進行編譯，例如在終端機輸入：

```bash
tsc index.ts
```

透過以上指令，我們就能將 `index.ts` 編譯成 `index.js`，而這個檔案就能被瀏覽器讀取哩。

### 小試身手

我們現在有一支 hello.ts 檔案，內容為：

```ts
function greet(person: string) {
	return `Hello, ${person}!`;
}

let user = "Billy";
console.log(greet(user));
```

如下圖所示，當我們在終端機輸入 `tsc hello.ts` 時，就會幫我們產生一支編譯好的 `hello.js`。
![hello.ts編譯為hello.js](img/typescript-start-1.png)

## TypeScript Config File

透過在終端機輸入 `npx tsc --init`，能讓我們在專案新增一支 tsconfig.json。
而這支檔案的相關設置，可以讓 TypeScript 編譯器（tsc）知道要怎麼處理專案中的 TypeScript 程式，例如要編譯哪些檔案、編譯後的 JavaScript 版本等。（我們可以根據專案需求來修改）

```bash
npx tsc --init
```

幾個常見的設定項目及用途：

```json
{
	"compilerOptions": {
		"target": "ES6", // 要編譯成哪個 JavaScript 版本（例如：ES6）
		"module": "CommonJS", // 預設使用 CommonJS 模組系統
		"strict": true, // 嚴格模式，預設是開啟的
		"outDir": "./dist", // 編譯後的檔案要放的位置
		"sourceMap": true // 生成對應的 source map
	},
	"include": ["src/**/*"], // 要編譯的檔案位置，以目前這個設置來說，是指 src 資料夾中的所有檔案
	"exclude": ["node_modules", "dist"] // 編譯要排除的資料夾，以這個設置來說會去忽略 node_modules 和 dist 資料夾
}
```

## 腳手架工具

在目前的前端開發中，使用腳手架工具（例如 Vite、Create React App）是相當常見的，這類工具幫我們配置許多環境設置，讓我們可以專注在程式碼的撰寫，而不太需要（或很少）去配置相關設定。

而使用這類工具時，我們也不需要再手動輸入 `npx tsc --init` 來生成 tsconfig.json 檔案，因為它已經在專案初始化時，幫我們做好相關配置。除此之外，也會幫我們處理編譯相關，所以我們也不需要手動去執行 `tsc xxx.ts` 這類指令，工具會幫我們處理這個部分。

舉例來說，當我們透過 Vite 初始化一個 React（TypeScript）專案時，它就會自動生成 tsconfig.json，並預設好各種配置選項，我們只需要根據需求去調整或增減設定即可。如下所示：

```json
{
	"compilerOptions": {
		"target": "ES2020",
		"useDefineForClassFields": true,
		"lib": ["ES2020", "DOM", "DOM.Iterable"],
		"module": "ESNext",
		"skipLibCheck": true,

		/* Bundler mode */
		"moduleResolution": "bundler",
		"allowImportingTsExtensions": true,
		"isolatedModules": true,
		"moduleDetection": "force",
		"noEmit": true,
		"jsx": "react-jsx",

		/* Linting */
		"strict": true,
		"noUnusedLocals": true,
		"noUnusedParameters": true,
		"noFallthroughCasesInSwitch": true
	},
	"include": ["src"]
}
```
