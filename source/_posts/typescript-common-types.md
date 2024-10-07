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

使用 TypeScript 來撰寫，就可以明確指定函式的 a、b 參數都必須是 number 型別，而回傳結果也是 number 型別，來達到數字相加的預期結果；如果我們將參數傳入不同型別（例如：字串），在編譯過程或者 VSCode 的功能上，就會提醒我們出現錯誤。而這樣的型別設置，就能在「開發階段」時就提醒我們型別出現錯誤了。

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

## 原始型別（Primitive Types）

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

## 陣列型別（Array Types）

陣列型別可以用來定義一組相同型別的元素。TypeScript 提供兩種常見的語法來做定義：

- `let 變數名稱: 型別[] = 陣列資料`
- `let 變數名稱: Array<型別> = 陣列資料`：這種方式是使用泛型來定義，這邊可約略先知道有這樣的方式，而關於泛型這個概念的詳細，會在後面章節補充。

第一種語法範例：

```ts
let numbers: number[] = [1, 2, 3]; // 定義一個 number 型別的陣列
numbers.push(4); // OK
numbers.push("5"); // 陣列內的項目型別，需符合我們定義好的型別。所以這邊 push 的字串型別會出現錯誤。
```

第二種語法範例：

```ts
let fruits: Array<string> = ["Apple", "Banana"]; // 定義一個 string 型別的陣列
fruits.push("Orange"); // OK
fruits.push(123); // 這樣會報錯，因為 123 不是字串
```

TypeScript 支援多維陣列的方式，如下方範例的二維陣列（內容補充）：

```ts
let weeklyMenu: string[][] = [["Chicken"], ["Beef"], ["Fish"]];

// 嘗試推入一個數字會報錯
weeklyMenu.push([33]); // Type 'number' is not assignable to type 'string'
```

### 元組型別（Tuple Type）

元組是 TypeScript 中的一種特殊陣列，它與一般陣列不同的是，元組這個概念允許我們在陣列中包含「不同型別」的元素，但每個元素的型別和順序都是被嚴格定義的（陣列長度也是）。

範例：

```ts
let person: [string, number, boolean];
person = ["Billy", 35, true]; // OK

// 錯誤
person = [123, 35, true]; // 第一個 123 數字型別不符合我們所定義的（字串）
person = ["Alice", 30, true, 111]; // 陣列長度超出原先定義的，所以這樣也會報錯
person = ["Alice", 30]; // 這樣也是錯誤的
```

如果有遇到特殊狀況，需要明確定義資料結構的順序和型別時，也許就會使用到元組這個概念。
（不過目前思考了過去專案，這樣狀況應該是不常見低～）

## 物件型別（Object Types）

物件型別可以用來定義物件的結構，確保每個屬性都有正確的型別。

```ts
let user: {
	name: string;
	age: number;
	gender: string;
	isActive: boolean;
} = {
	name: "Billy",
	age: 33,
	gender: "male",
	isActive: true,
};

console.log(user.name); // Billy
user.age = 35; // 這樣不會出錯，因為 35 這個值是數字型別

// 會出錯
user.age = "我的年齡是35";
user.phone = "0900-999-999"; // 這樣會出錯，因為並沒有定義 phone 屬性
```

### 選擇性屬性（Optional Properties）

有時我們並不需要每個屬性都是「必須」提供的，像是有些資訊可以依照使用者需求（喜好）來提供，這時我們可以使用 `?` 來讓某些屬性變為選擇性屬性。

user 相關範例如下，因為我們將 `phoneNumber?` 多設置了選擇性屬性，所以不論在 user1 沒有提供 `phoneNumber` 的資訊，或者 user2 有提供 `phoneNumber` 的資訊，都不會出現 TypeScript 的相關警告。

```ts
let user1: {
	name: string;
	email: string;
	age: number;
	phoneNumber?: string; // 這邊將 phoneNumber 設置為選擇性屬性
} = {
	name: "John Doe",
	email: "john@example.com",
	age: 30,
};

let user2: {
	name: string;
	email: string;
	age: number;
	phoneNumber?: string;
} = {
	name: "Jane Smith",
	email: "jane@example.com",
	age: 28,
	phoneNumber: "0987-654321",
};

console.log(user1.phoneNumber); // undefined，因為 user1 沒有提供電話號碼
console.log(user2.phoneNumber); // "0987-654321"
```

### 唯讀屬性（Readonly Properties）

除了在物件使用選擇性屬性，TypeScript 也提供了 `readonly` 關鍵字，讓某些屬性只能讀取，不能再修改。

範例：

```ts
let point: {
	readonly x: number;
	readonly y: number;
} = {
	x: 10,
	y: 20,
};

console.log(point.x); // 10

point.x = 15; // 這樣會出錯，因為 x 是唯讀屬性
```

## 函式型別（Function Types）

目前進度
（除了課程一般的用法，也需注意 `let sum: (a: number, b: number) => number;` 這個用法，先小提一下）

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

---

## 任意資料型態陣列 any

有些時候我們無法確定資料型別是什麼，這時就可以用 any。
不過既然我們已經在撰寫 TypeScript，就盡量謹慎使用 any。

```ts
const jsonString = '{"x":10,"y":20}';
const parsedData = JSON.parse(jsonString);

// 範例二
// 使用 fetch 取得資料，返回的資料類型為 any
async function fetchUserData(url: string) {
	try {
		const response = await fetch(url);
		const data = await response.json(); // data 是 any 類型
		return data;
	} catch (error) {
		console.error("取得資料時出錯:", error);
		return null;
	}
}

// 呼叫函式並處理返回的資料
async function handleUserData() {
	const url = "https://api.example.com/user";
	const userData = await fetchUserData(url); // userData 是 any 類型
	if (userData) {
		console.log(userData); // 直接輸出取得的資料
	}
}

handleUserData();
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
