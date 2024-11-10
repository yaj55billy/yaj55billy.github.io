---
title: TypeScript 型別斷言
date: 2024-11-10 11:20:26
tags: [JavaScript, TypeScript]
categories: [前端 TypeScript 系列]
excerpt: 先前在 TypeScript 型別註記與型別推論文章中，因為篇幅關係，所以沒有討論到型別斷言，而這篇要來補上這個觀念。
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
- 型別斷言：依照我們自身的判斷，明確告訴 TypeScript 編譯器**某表達式之結果應該是什麼型別**。也就是我們斷定的型別，去「**覆蓋**」TypeScript 編譯器所推斷的型別。

## 型別斷言的使用情境

當程式沒辦法推斷出某表達式的運算結果之型別時，我們就可以使用型別斷言。以下舉例幾個情境：

### 未知結果的函式

假設我們有個函式 `fetchDataFromLibrary`，因為它可能根據某個外部資源獲取資料，所以返回的型別不確定；不過我們可能從套件中的文件得知它會回傳某種特定型別（下方範例中是斷言為 `string`）。

```tsx
function fetchDataFromLibrary() {
	// ...
	// 此函式模擬從外部函式庫取得資料
	return "Library Data";
}

// 使用型別斷言來告訴 TypeScript 返回值是 `string`
const dataString = fetchDataFromLibrary() as string;

// 另一種斷言寫法
const dataStringAlt = <string>fetchDataFromLibrary();
```

### HTML DOM

在沒有使用前端框架的 TypeScript 環境中，當我們取 HTML DOM 元素並嘗試操作時，TypeScript 會推斷此元素返回的型別為 `HTMLElement | null`，所以會出現紅字來提醒。如下範例所示：

```tsx
const element = document.getElementById("my-element");
element.innerText = "Hello"; // 'element' is possibly 'null'.
```

為了避免此錯誤提醒，我們可以用型別斷言告訴 TypeScript 該元素一定是 `HTMLElement`。不過以這個範例來說，在考量到程式的保護性，還是會建議加上`if(element)` 的判斷，以防止真的沒取到元素。

```tsx
const element = document.getElementById("my-element") as HTMLElement;

if (element) {
	// 判斷
	element.innerText = "Hello";
} else {
	console.log("找不到元素");
}
```

### API 返回的數據

在開發的過程中，有時會需要串接 API 或第三方套件，因爲 API 返回的數據無法明確推斷（通常會推斷為 `any`），這時我們可以使用型別斷言來告訴 TypeScript 預期的數據型別。

下方範例中，我們模擬從 [Random User API](https://randomuser.me/) 獲取使用者資料，而用 `fetchDataWithoutAssertion`、`fetchDataWithAssertion` 這兩個函式來比對是否使用型別斷言的狀況。

（以下有使用到 `interface` 的寫法，不過在這個 TypeScript 系列中還未提到，所以先簡單提一下定義，它是用來告訴 TypeScript 某個物件會包含哪些屬性跟方法，以及每個屬性的型別，可以把它想像成一個預先架構或藍圖。 不過在下方的範例中，可以先專注在兩個函式所比對的內容，來了解型別斷言的觀念。）

```tsx
// interface 定義了從 Random User API 回傳的資料結構
interface RandomUser {
	gender: string;
	name: {
		title: string;
		first: string;
		last: string;
	};
	email: string;
	picture: {
		large: string;
		medium: string;
		thumbnail: string;
	};
}

interface RandomUserResponse {
	results: RandomUser[];
	info: {
		seed: string;
		results: number;
		version: string;
	};
}

// 未使用型別斷言的情況
async function fetchDataWithoutAssertion() {
	const response = await fetch("https://randomuser.me/api/");
	const data = await response.json(); // data: any
	const user = data.results[0]; // user: any

	// 以下操作都不會有編譯時期的錯誤提示，因為 any 型別允許所有操作
	console.log(user.nameTypo.first); // 不會有錯誤提示，但執行時會出錯
	console.log(user.picture.extralarge); // 不會有錯誤提示，但執行時會出錯
}

// 使用型別斷言的情況
async function fetchDataWithAssertion() {
	const response = await fetch("https://randomuser.me/api/");
	const data = (await response.json()) as RandomUserResponse; // data: RandomUserResponse
	const user = data.results[0]; // user: RandomUser

	// 以下程式碼會在編譯時就報錯，可以提前發現問題
	console.log(user.nameTypo.first); // TS 編譯錯誤：Property 'nameTypo' does not exist
	console.log(user.picture.extralarge); // TS 編譯錯誤：Property 'extralarge' does not exist

	// 正確的使用方式
	console.log(user.name.first); // ✓ 正確
	console.log(user.picture.large); // ✓ 正確
}
```

- **未使用型別斷言**：在 `fetchDataWithoutAssertion` 函式中，`data` 被視為 `any` 型別，所以即便後續的屬性名稱有錯誤，因為 TypeScript 編譯器無法提供屬性或方法的檢查，所以也不會有編譯時期的錯誤提示，需要到執行時期才能知道錯誤。
- **使用型別斷言**：在 `fetchDataWithAssertion` 函式中，通過 `as RandomUserResponse` 將 `data` 斷言為特定型別，TypeScript 能在編譯時進行屬性跟方法的檢查。如果後續引用了不存在的屬性，如範例中看到的 `nameTypo`、`extralarge` ，在編譯時期就能直接看到錯誤提示。

## 結論

當程式沒辦法推斷出某表達式的運算結果之型別時，我們可根據一些資訊來適當加上型別斷言；不過使用斷言也較會有**人為**的錯誤，因此需比較謹慎。

最後引用 [<讓 TypeScript 成為你全端開發的 ACE！>](https://www.tenlong.com.tw/products/9789864344895?list_name=srh) 2-17 頁面的範例：

```tsx
function returnsAny(): any {
	return 123;
}

let something = returnsAny() as string;
```

如上方範例所示，程式從此會認定變數 `something` 是字串型別，即使在這段程式碼中，變數 `something` 實際的型別是數字。而這段程式碼在 TypeScript 不會出現任何錯誤訊息，完全是人為的錯誤哩！

參考資料：

- 六角學院 TypeScript 30 天課程
- 書籍：[<讓 TypeScript 成為你全端開發的 ACE！>](https://www.tenlong.com.tw/products/9789864344895?list_name=srh)

---

Photo by <a href="https://unsplash.com/@safarslife?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Safar Safarov</a> on <a href="https://unsplash.com/photos/turned-on-gray-laptop-computer-MSN8TFhJ0is?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
