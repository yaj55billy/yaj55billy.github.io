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

這一篇文章，主要來筆記 TypeScript 的基本型別；在進到正式內容前，也先透過一個簡單範例，來說明 TypeScript 是怎麼幫助我們減少開發錯誤的。

以原本 JavaScript 變數的特性來說，它可以被賦予任何的資料型態，在程式不複雜時，這樣的自由跟彈性比較不會帶來什麼問題；但如果程式開始變複雜時，就容易會有意外的狀況（Bug）。如下範例：

```js
// 在這個函式中，我們期望回傳「數字」相加的結果
function addFn(a, b) {
	return a + b;
}

let a = 10;
let b = 5;
console.log(addFn(a, b)); // 這邊會如我們預期，是數字與數字相加的結果 10+5 = 15

// ...
// ...假設經歷很長的程式碼（程式變得複雜）

a = "10"; // 不小心將 a 變數型別設定成「字串」

// 當我們要再次運算時，"10" + 5 就會被轉型成字串 "105"
console.log(addFn(a, b));
```

從上方範例中，我們可以看到程式碼偏離了預期（數字相加、回傳數字），而這樣就會導致開發的錯誤。

使用 TypeScript 來撰寫，就可以明確指定 a、b 參數都必須是 number 型別，而回傳結果也是 number 型別，來達到數字相加的預期結果；如果我們將參數傳入不同型別（例如：字串），在編譯過程或者 VSCode 的功能上，就會提醒我們出現錯誤。而這樣的型別設置，就能有效避免程式出現錯誤。

```ts
function addFn(a: number, b: number): number {
	// 參數型別、函式回傳型別都設置成 number
	return a + b;
}

let a: number = 10;
let b: number = 5;
console.log(addFn(a, b)); // 預期結果 10+5=15

// ...
// ...假設經歷很長的程式碼（程式變得複雜）

// 如果不小心設置 a 為字串，在編譯時就會報錯。
a = "10"; // Error: Type 'string' is not assignable to type 'number'.
```

## 原始型別（Primitive）

在 JavaScript 中有七種原始型別，分別是：

- String
- Number
- Boolean
- Null
- Undefined
- Symbol：ES6/2015 才有，可用於建立獨一無二的值
- BigInt：ES11/2020 才有，用於數值非常大的狀況
  （Symbol、BigInt 基本會較少使用到，我自己目前在專案開發中也沒有用過）

宣告變數加上 TypeScript 的型別定義時，規則是這樣的：`let 變數名稱:資料型態 = 資料`

```ts
// String
let text: string = "我是字串"; // 資料型態小寫就可以了

// Number
let age: number = 30;
age = 40; // 這樣如同預期設置的數字型別，不會產生錯誤
age = "我年齡是..."; // 這樣就會產生 編譯錯誤

// Boolean
let isTrue: boolean = true;
isTrue = false; // 這樣如同預期設置的布林型別，不會產生錯誤
isTrue = "true"; // 這樣就會產生 編譯錯誤

// Null
let money: null = null;

// Undefined
let notAssigned: undefined = undefined;

// Symbol
// 使用 Symbol() 時，內部會建立一個獨特的 id，即使是兩個 key 相同，也是代表不相等的
let dataKey1: symbol = Symbol("my-data-key");
let dataKey2: symbol = Symbol("my-data-key");
console.log(dataKey1 === dataKey2); // false

// BigInt
let bigNumber: bigint = BigInt(Number.MAX_SAFE_INTEGER);
```

## 陣列型別

陣列型別的宣告方式：
最後一個宣告方式 list3，是不同意思（會限定陣列長度），這個需確認是不是元祖的意思

在陣列這個段落，我會說明一般陣列宣告方式，以及和元祖的不同

```tsx
let list: string[] = ["a", "b", "c"]; // 要一個裡面資料型別為 string 的陣列
let list2: Array<string> = ["a", "b", "c"]; // 這個 Array 裡面是 string 的資料型別

let list3: [string, string, string] = ["a", "b", "c"]; // 跟前兩者不同

list = ["a"];
// ...
```

```ts
x = true; // compile time error
let y: string[] = [3, "a", "b"]; // compile time error

let grades: number[] = [60, 70, 80];
console.log(grades);
// ...
grades.push("hello"); // 編譯會產生錯誤 Argument of type 'string' is not assignable to parameter of type 'number'.

let a: Array<boolean>;
a = [true, false];
console.log(a); // 這樣就不會錯誤
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

## 任意資料型態陣列 any

盡量不要使用，畢竟我們已經在用 TypeScript

## ...

從動態語言跟靜態語言的概念，
來看 TypeScript 的型別註記跟型別推論
