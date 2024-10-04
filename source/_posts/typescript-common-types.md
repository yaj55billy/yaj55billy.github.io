---
title: TypeScript 基本型別
date: 2024-10-03 19:15:59
tags: [JavaScript, TypeScript]
categories: [前端]
excerpt: 關於 TypeScript 基本型別與用法
index_img: "/img/typescript.jpg"
# banner_img: "/img/typescript.jpg"
---

## 前言

這一篇文章，主要來筆記 TypeScript 的基本型別；
而在進到正式內容前，也先透過簡單範例，來說明 TypeScript 是怎麼幫助我們減少開發錯誤的。

JavaScript 變數的特性
變數可以賦予任何的資料型態，在程式不複雜時，這樣的彈性並不會帶來什麼問題；
不過如果程式開始變複雜時，就容易有些意外的 Bug

```js
function add(a, b) {
	return a + b;
}

// 我們原本期望 `a` 和 `b` 都是數字
let a = 10;
let b = 5;

console.log(add(a, b)); // 這裡會正常輸出 15

// 但如果在某處不小心將 `a` 的型別變成字串
a = "10";

console.log(add(a, b)); // 預期是 15，實際輸出 "105"
```

在這個 TypeScript 版本中，我們明確指定了 a 和 b 都必須是 number 型別。如果後面嘗試將 a 改為字串，編譯器會立即報錯，提醒我們變數的型別不符合要求，從而防止潛在的 Bug。

這樣的型別檢查能夠在開發階段避免類似的錯誤，提升代碼的健壯性和可維護性。

```ts
function add(a: number, b: number): number {
	return a + b;
}

// 宣告 `a` 和 `b` 都是數字
let a: number = 10;
let b: number = 5;

console.log(add(a, b)); // 正常輸出 15

// 如果不小心將 `a` 設為字串，TypeScript 會報錯
// a = "10"; // Error: Type 'string' is not assignable to type 'number'.

console.log(add(a, b)); // 保證後續運算的型別正確
```

---

## 原始型別（Primitive）

JS 有七種原始型別

- 數字 number
- 字串 string
- 布林值 boolean
- 特殊型別：null 和 undefined
- Symbol（若提到更深入的 ES6 特性，可以簡單介紹）
- BIGInt
  （這邊可參考課程內容）

基本語法 `let 變數名稱:資料型態 = 資料`

```ts
// 這邊是範例
let x: number = 3;
x = true; // compile time error
let y: string[] = [3, "a", "b"]; // compile time error

let s: string;
s = "我是字串";
console.log(s); // 不會錯誤
s = true;
console.log(s); // 這樣就會產生錯誤

// ...

let grades: number[] = [60, 70, 80];
console.log(grades);
// ...
grades.push("hello"); // 編譯會產生錯誤 Argument of type 'string' is not assignable to parameter of type 'number'.

let a: Array<boolean>;
a = [true, false];
console.log(a); // 這樣就不會錯誤
```

## 陣列型別

陣列型別的宣告方式：
最後一個宣告方式 list3，是不同意思（會限定陣列長度），這個需確認是不是元祖的意思

```tsx
let list: string[] = ["a", "b", "c"]; // 要一個裡面資料型別為 string 的陣列
let list2: Array<string> = ["a", "b", "c"]; // 這個 Array 裡面是 string 的資料型別

let list3: [string, string, string] = ["a", "b", "c"]; // 跟前兩者不同

list = ["a"];
// ...
```

## 物件型別

（可以再看看物件型別的範例）

```jsx
let leo: {
	name: string;
	age: number;
	gender: string;
	height: number;
	weight: number;
} = {
	name: 'xxx';
	age: 18;
	gender: '男';
	height: 180;
	weight: 70;
}

// 造成重覆的程式碼
let xxx: {
	name: string;
	age: number;
	gender: string;
	height: number;
	weight: number;
} = {
	name: 'xxx';
	age: 18;
	gender: '男';
	height: 180;
	weight: 70;
}
```

## 函式型別

```tsx
function sum(a, b) {
	return a + b;
}

console.log(sum("acdsdcds", null)); // 使用者的傳入，可能會超出我們的預期

function sum(a: number, b: number) {
	// 較少這樣使用
	return a + b;
}

let sum: (a: number, b: number) => number; // 最後一個 number 是回傳值的型別

sum = function (a, b) {
	return a + b;
};

// ...
// 重覆使用時，就會依循上方的宣告
sum = function (c, d) {
	return c + d;
};
```

## 型別別名

給物件型別一個名字，讓開發者容易理解，也不用重覆宣告

```jsx
type Human = { // 這邊要用 =
	name: string; // 這邊也可以用逗號結尾，跟物件一樣。但為了區別，也可以用分號
	age: number;
	gender: string;
	height: number;
	weight: number;
}

let leo: Human = {
	name: 'xxx';
	age: 18;
	gender: '男';
	height: 180;
	weight: 70;
}

let billy: Human = {
	// ...
}

// 用在陣列也是一樣的
type Position = [number, number];
let a: Position = [0, 0];

```

函式型別的型別別名：

```tsx
type CalculatorFn = (a: number, b: number) => number;

const sum: CalculatorFn = (a, b) => {
	return a + b;
};

const mutiple: CalculatorFn = (a, b) => {
	return a * b;
};
```

---

## 聯合型別

同一個變數，可能為 A、也可能為 B 型別

```tsx
type StyleValue = number | string; // 以直線去分隔型別

const width: StyleValue = "100px";

const zIndex: StyleValue = 9999;

let aaa: number | string;
aaa = 111;
aaa = []; // 這樣就會出錯
```

取得 DOM 元素，本身也是種聯合型別，可能會有 HTMLElement 或者 null

```tsx
const element = document.getElementById("xxx"); // 可能會有 HTMLElement | null

// element.innerText = ... 沒有判斷直接使用會出錯

if (element) {
	// 不為 null 時，才做賦予
	element.innerText = "OOO";
}

// 驚嘆號表示一種保證，開發者保證有這個 HTMLElement，但不建議這樣使用
element!.innerText = "OOO";

// 可選串連，如果為 null 就不會執行 after()，如果不是 null 就會執行 after()
element?.after();
```

```jsx
type Status = document.get;
```

### 型別別名 + 聯合型別

```jsx

```

聯合型別可以限縮情境

## enum 列舉

也是限縮情境用，不過跟聯合型別不太一樣。

## 任意資料型態陣列 any

盡量不要使用，畢竟我們已經在用 TypeScript
