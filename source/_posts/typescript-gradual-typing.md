---
title: TypeScript 型別註記與型別推論
date: 2024-10-20 12:28:41
tags: [JavaScript, TypeScript]
categories: [前端 TypeScript 系列]
excerpt: 這篇主要探討 TypeScript 的核心，關於型別的註記跟推論
index_img: "/img/typescript.jpg"
banner_img: "/img/article-banner.jpg"
---

（TypeScript 的系列文，其說明跟範例都是跟著預設將嚴謹模式打開 `strict: true;`，如果將嚴謹模式關閉，則可能會跟以下說明與範例產生不同結果。畢竟我們已經在使用 TypeScript，就不建議把預設的嚴謹關掉哩！）

## 在型別註記與推論之前

在進入 TypeScript 的核心概念——型別註記與型別推論之前，我們先簡單探討靜態型別語言（Statically Typed）與動態型別語言（Dynamically Typed）的差異。

我們在學習 JavaScript 時，應該對這句話不陌生：「JavaScript 是動態型別語言，變數的型別會在程式執行期間才確定」。而動態型別語言的特色在於**變數的型別會在程式執行時期（Run-time）根據賦予的值來決定**，而不是在變數宣告時就確定其型別。這種類型的語言具有靈活性，但也可能帶來潛在的型別錯誤。

以下是一個簡單的 JavaScript 範例，說明動態型別的特性。在範例中我們可以將變數 `variable` 指派為不同的值，但要在程式執行時期才會知道其型別。

```js
let variable = 10;
console.log(typeof variable); // 輸出: "number"

variable = "Hello!";
console.log(typeof variable); // 輸出: "string"

variable = true;
console.log(typeof variable); // 輸出: "boolean"
```

靜態型別語言的特性則相對不同。靜態型別語言會在**編譯時（Compilation）根據程式內宣告的型別來監控其狀態**，這樣的特性可在編譯過程中，捕捉到潛在的型別錯誤。

以下是 C# 的一個簡單範例，說明靜態型別語言的特性。從範例中我們在 `number`、`text` 這兩個變數之前使用了 `int`、`string` 等文字來定義型別，所以像 `text = 555;` 這行就會在編譯時產生錯誤，因為 `text` 這個變數已經被定義是字串型別，不能賦予整數（數字）給它。

```C#
class Program
{
  static void Main()
  {
    int number = 10; // 宣告一個整數型別的變數 number
    Console.WriteLine("Number: " + number);

    string text = "Hello, World!";
    Console.WriteLine("Text: " + text);

    text = 555; // 這行會在編譯時產生錯誤
  }
}
```

那麼 TypeScript 是屬於動態語言還是靜態語言呢？在這個系列的上一篇[TypeScript 基本型別](https://yaj55billy.github.io/post/typescript-common-types.html)中，我們已經知道怎麼在變數加上型別宣告（如下範例），而這樣先以**文字來宣告其型別，並且會監控其型別狀態**的特性，可以知道 TypeScript 是有靜態語言的特性。

```ts
let text: string = "我是字串";
let age: number = 30;

text = 30; // 會出錯
age = "這是..."; // 會出錯
```

不過當我們嘗試把型別宣告（註記）給拔除，而把鼠標移動到變數上時，又能看出變數 `text` 的型別，而這樣**根據賦予之值 `"我是字串"` 來推斷出型別**，TypeScript 似乎也有動態型別的特性。（如下方範例跟圖片所示）

```ts
let text = "我是字串";
let age = 30;

text = 30; // 依然會根據推斷，提到這樣不同的型別指派會出錯
age = "這是..."; // 依然會根據推斷，提到這樣不同的型別指派會出錯
```

![關於 TypeScript 型別推論](/img/typescript-gradual-typing-1.png)

基於以上內容，我們可以總結 TypeScript 同時有靜態語言跟動態語言的特性，而結合這兩種特性會被稱為**漸進式型別系統（Gradual Typing）**。當我們明確註記型別時，TypeScript 會像靜態型別語言，在編譯階段幫我們檢查型別並防止一些型別錯誤；而如果我們沒有註記型別時，TypeScript 則會根據賦予的值幫我們做型別推論，這類似動態型別語言的靈活性。

## 型別註記（Type Annotation）與型別推論（Type Inference）

在上個段落的討論中，我們探討了靜態語言跟動態語言的特性，並總結出 TypeScript 擁有這兩種特性（漸進式型別系統）。所以我們能在 TypeScript 的撰寫中，根據不同狀況去適當選擇**嚴謹的型別註記**，又或者我們能選擇**靈活簡潔的型別推論**。

這邊也引用<讓 TypeScript 成為你全端開發的 ACE！>這本書 P.1-13 對這兩者的定義：

> 型別註記：為對變數或表達式進行文字敘述上的型別宣告動作。
> 型別推論：則是變數根據被賦予的值之型別來代表該變數之型別；而表達式則是經運算結果的值之型別來代表整個表達式最後的型別結果。

接下來，我們將探討這兩者在不同情境下的使用，以及可能需要注意的部分。

### 原始型別的註記跟推論

這個段落主要會來探討原始型別的推論，而原始型別註記的相關內容，因為比較單純些，所以這邊就不再多提及。相關內容可參考[TypeScript 基本型別 - 原始型別](https://yaj55billy.github.io/post/typescript-common-types.html#%E5%8E%9F%E5%A7%8B%E5%9E%8B%E5%88%A5%EF%BC%88Primitive-Types%EF%BC%89)。

如下範例所示，即使我們沒有註記上型別，TypeScript 也會根據被賦予的值來推斷出型別：

- 變數 `text` 會被推論為字串型別
- 變數 `age` 會被推論為數字型別
- 變數 `isTrue` 會被推論為布林型別
- 變數 `money` 會被推論為 null 型別
- 變數 `notAssigned` 會被推論為 undefined 型別

```ts
let text = "我是字串";
let age = 30;
let isTrue = true;
let money = null;
let notAssigned = undefined;
```

在 TypeScript 的環境上，即使型別是被「推論」出來，後續任何指派到該變數的值，也需符合該變數首次被推論出來的型別。如下方範例，`text` 被推論是字串型別，所以在 `text = 33` 這樣的指派時會出現錯誤訊息：`Type 'number' is not assignable to type 'string'.`。

```ts
let text = "我是字串"; // 被推論是字串型別
text = "這是一句話"; // 這樣是 OK 的
text = 33; // Type 'number' is not assignable to type 'string'.
```

然而，如果變數在宣告時沒有被賦予任何值，TypeScript 的編譯器就無法推論其型別，因此會用 `any` 型別來代替。`any` 型別意味著這個變數可以被賦予任何型別，而不會產生型別相關錯誤。這種情況稱為「遲滯性指派」。如下範例跟圖片所示：

```ts
let num; // 會被推斷是 any 型別

// 以下指派都不會有錯誤，因為 any 型別
num = 123;
num = "字串";
num = true;
num = [1, 2, 3];
```

![遲滯性指派的 any 型別](/img/typescript-gradual-typing-2.png)

在這個段落的結尾之前，我們來看看被推論成聯合型別的狀況。當變數的型別推論，遇到一些判斷（條件敘述、三元運算）而有所不同時，TypeScript 編譯器會幫我們根據判斷的不同狀況，分別推論匯集成聯合型別。如下範例所示，`unionValue` 變數會被推論為 `string | number`。

```ts
let unionValue = Math.random() > 0.5 ? "我是字串" : 200;
// TypeScript 會推論 unionValue 的型別為 string | number
```

### 物件型別的註記跟推論

#### 物件的型別推論

首先我們先來討論物件的型別推論，如下範例跟圖片，當我們把滑鼠移動到 `user` 時，會看到 TypeScript 幫我們推論出物件的結構跟各個屬性型別。

```ts
let user = {
	name: "Billy",
	age: 33,
	gender: "male",
	isActive: true,
};
```

![物件的型別推論](/img/typescript-gradual-typing-3.png)

對於這樣的推論，我們接續著探討如果對於物件內屬性的值做調整，或者去調整（覆寫）整個物件時，會發生什麼狀況？

對於物件內屬性值的調整，需符合原先定義的型別，所以 `user.name = 333` 會出現錯誤：`Type 'number' is not assignable to type 'string'.`。

```ts
let user = {
	name: "Billy",
	age: 33,
	gender: "male",
	isActive: true,
};

user.name = "John";
user.name = 333; // Type 'number' is not assignable to type 'string'.
```

而整個物件的覆寫，如果符合原先物件結構跟屬性型別，則不會有錯誤；但如果在原先的結構定義，有少或多了屬性，TypeScript 會提醒我們有錯誤，例如少了 `age` 屬性時，將會得到這一串錯誤訊息：`Property 'age' is missing in type '{ name: string; gender: string; isActive: false; }' but required in type '{ name: string; age: number; gender: string; isActive: boolean; }'.`。

```ts
let user = {
	name: "Billy",
	age: 33,
	gender: "male",
	isActive: true,
};

user = {
	// 這樣是 OK 的
	name: "Eve",
	age: 40,
	gender: "male",
	isActive: false,
};

user = {
	// 多或少了屬性，則會有錯誤提醒
	name: "Eve",
	// age: 40,
	gender: "male",
	isActive: false,
	// mail: "xxx@gmail.com"
};
```

在物件的型別推論中，最後我們來看刪除屬性的狀況。當我們想刪除 `user.name` 時會出現這樣的錯誤訊息：`The operand of a 'delete' operator must be optional.`，告訴我們這屬性必須要是可選的，這個部分將在稍後補充。

（目前這篇文章的 TypeScript 版本為 5.6，在版本 4 之前，這樣刪除物件中某個屬性的動作，並不會出現錯誤提醒，而刪除後再次查看這個屬性時，也不會如預期是 `undefined` 型別，**這是滿奇怪的地方**；而版本 4.x 之後，TypeScript 有對這個部分做調整，關於這個部分可參考[Announcing TypeScript 4.0](https://devblogs.microsoft.com/typescript/announcing-typescript-4-0/)）

```ts
let user = {
	name: "Billy",
	age: 33,
	gender: "male",
	isActive: true,
};

delete user.name; // The operand of a 'delete' operator must be optional.
```

#### 物件的型別註記

再來要討論物件型別的註記部分，如下方範例所示，這樣的型別註記方式我們應該不陌生。
這邊也補充一個小細節，在寫 JavaScript 的物件時，每一行屬性跟值我們是以「逗號」作為分隔；而在 TypeScript 先定義物件結構跟屬性時，使用分號或逗號都是被允許的。（我自己目前在學習是比較偏好分號，可以跟原先物件做個區隔）

```ts
let user: {
	name: string; // 這邊用分號或逗號都可以
	age: number;
	gender: string;
	isActive: boolean;
} = {
	name: "Billy", // 這邊一定要逗號
	age: 33,
	gender: "male",
	isActive: true,
};
```

而對於物件內屬性的值做調整，或者要覆寫整個物件時，這個部分與物件型別的推論是一樣的。

- 對於物件內屬性值的調整，需符合原先屬性所定義的型別。
- 對於整個物件的覆寫，如果符合原先物件結構跟屬性所定義的型別，則不會產生錯誤。

如下範例所示，這邊就不再贅述：

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

user.age = 40; // 這樣是 OK 的！
user.age = true; // Type 'boolean' is not assignable to type 'number'.

user = {
	// 結構跟型別與原先ㄧ致，所以是 OK 的！
	name: "John",
	age: 30,
	gender: "male",
	isActive: false,
};

user = {
	// Property 'age' is missing in type '{ name: string; gender: string; isActive: false; }' but required in type '{ name: string; age: number; gender: string; isActive: boolean; }'
	name: "John",
	// 少了 age 屬性（多了屬性或少了屬性都會出現錯誤）
	gender: "male",
	isActive: false,
};
```

在物件型別的註記中，最後要來討論刪除屬性的部分。稍早在物件型別的推論，如果我們想刪除物件某個屬性（例如：`user.name`），則會出現要把這個**屬性設置為可選**的錯誤訊息，這個部分在物件型別的註記也是一樣的。

而當我們將某個物件屬性加上可選後，對這個屬性做 `delete` 就不會出現錯誤。以下方範例的 `user.name` 來說，加上可選屬性後，TypeScript 會幫我們做聯集型別，允許是原先的字串型別，或者是未定義的 `undefined`（如下方截圖）。

```ts
let user: {
	name?: string;
	age: number;
	gender: string;
	isActive: boolean;
} = {
	name: "Billy",
	age: 33,
	gender: "male",
	isActive: true,
};

delete user.name; // 這邊就不會出現錯誤

// 沒問題的狀況
user.name = "John";
user.name = undefined;

// 錯誤設置
user.name = 33; // Type 'number' is not assignable to type 'string'.
user.name = true; // Type 'boolean' is not assignable to type 'string'.
```

![物件屬性加上可選](/img/typescript-gradual-typing-4.png)

### 函式型別的註記跟推論

關於函式型別的註記跟推論，主要會分為參數（輸入）以及回傳值（輸出）兩個部分。如果我們都沒有進行型別註記，在大多數狀況下，TypeScript 會無條件將函式的參數**推論為 any 型別**，而函式的回傳值則會看 **return 敘述式回傳結果值的型別**。

#### 函式型別的推論

如果函式沒有任何參數參與，則 TypeScript 會單純根據 return 敘述回傳值的型別來做推斷。
特別提及下方範例的第三個跟第四個函式，如果函式內沒有 return 字樣，或者單純只有 return 而沒有帶上回傳值時，我們會看到 `void` 來表示函式回傳結果為空（無意義），如：`function logMessage() :void`。

```ts
function getRandomNumber() {
	// function getRandomNumber(): number
	return Math.random();
}

function getFilterValue() {
	// function getFilterValue(): string | number
	const value = Math.random();

	if (value > 0.5) {
		return value; // 回傳數字結果
	} else {
		return value.toString(); // 回傳字串結果
	}
}

function logMessage() {
	// function logMessage() :void
	console.log("沒有輸出，僅僅只是 log 一些訊息");
}

function returnEmpty() {
	// function returnEmpty() :void
	return;
}
```

如下方範例，當函式有參數但沒有被註記型別時，TypeScript 會出現這樣的錯誤訊息來提醒我們：`Parameter 'input' implicitly has an 'any' type.`，也就是 input 參數被隱含成 any 型別。函式參數（輸入）在本質上是無法被推論的，因為參數的型別是由開發者所定義。

```ts
function echo(input) {
	return input;
}

// function echo(input: any): any
echo(123);
echo("Hello");
echo([1, 2, 3]);
```

![函式參數沒有註記型別](/img/typescript-gradual-typing-5.png)

#### 函式型別的註記

如上個段落所提，如果函式的參數（輸入）沒有被註記時，在大多數狀況會無條件被推論成 any 型別，而如果這個部分不加以限縮，則容易造成一些錯誤發生，所以建議函式參數要註記型別；而函式的回傳值（輸出）是看 return 所回傳的結果值，這個部分可以看狀況來決定要主動註記或者讓 TypeScript 推論。

這邊也再複習一下，關於函式型別的註記，以下這幾種方式都能讓函式被註記成：`(a: number, b: number) => number`。

```ts
let addFn: (a: number, b: number) => number = function (a, b) {
	return a + b;
};

let addFn2 = function (a: number, b: number): number {
	return a + b;
};

function addFn3(a: number, b: number): number {
	return a + b;
}
```

關於函式的回傳值是否要註記，這個部分是看狀況而定，像是函式是否複雜、回傳值是否為 any 型別、團隊習慣考量等等。
引用上方範例的 `addFn3` 函式來說，因為回傳的是數字相加的結果，而參數也有註記為數字，所以像這樣單純的函式，其回傳值就可以讓 TypeScript 幫我們推論。

```ts
function addFn3(a: number, b: number) {
	return a + b;
}
```

這邊也模擬一下，如果是函式內容稍複雜的狀況，就可以考慮註記上型別。
（這邊主要是提及概念，因為這個範例其實還是能讓 TypeScript 幫我們去推論出來，~~抱歉暫時沒想到更好的範例~~）

```ts
function transformData(input: number[]): string[] | null {
	if (input.length === 0) {
		return null;
	}

	return input.map((num) => {
		if (num < 0) {
			return `負數: ${Math.abs(num)}`;
		}
		return `正數: ${num}`;
	});
}
```

最後也引用<讓 TypeScript 成為你全端開發的 ACE！>一書的作者 Maxwell 大大提到他自己的開發習慣：「開發時會盡量註記函式的輸出型別，而函式在實踐完成後，如果沒有太多功能要擴充或改寫的需求，而且函式在命名上的辨識度夠高時，就會主動將該函式的輸出型別拔除，讓 TypeScript 來處理推論」（書頁 3-39）

### 陣列型別的註記跟推論

因為這個段落所要提及的內容較少，所以會將陣列型別的註記跟推論一起討論。

如下範例所示，在第一個範例的推論是容易理解的；而第二個範例當我們在陣列塞入各種型別時，TypeScript 會將出現的各種型別都聯集在一起。
像第一個範例這樣只存放單個型別，稱為同質性陣列；而第二個範例這樣放不同型別，稱為異質性陣列。大多在開發上，都是以同質性陣列為主，畢竟異質性陣列還需根據不同型別做判斷跟處理。（我目前在開發上，也還沒遇過異質性陣列的狀況）

```ts
// 推論 >> const fruits: string[];
const fruits = ["apple", "banana", "cherry", "date", "elderberry"];

// 推論 >> const arr: (string | number | boolean | number[])[]
const arr = [123, "Hello", false, [222, 333]];
```

另外在空陣列的狀況，如果我們沒有註記則會被 TypeScript 推論成 `any[]`，因此為了避免這個狀況，會建議先進行註記哩。

```ts
// const emptyArr: any[]
const emptyArr = [];

// 有註記
const emptyArr: number[] = [];
```

### 何時使用註記？何時使用推論？

| 項目     | **型別註記**                                      | **型別推論**                                       |
| -------- | ------------------------------------------------- | -------------------------------------------------- |
| 定義     | 明確指定變數、參數或回傳值的型別                  | TypeScript 根據賦予的值自動推斷型別                |
| 優點     | 型別明確、安全                                    | 簡潔                                               |
| 缺點     | 過度使用會造成程式碼冗長                          | 需依賴推斷，可能會有意外的型別錯誤                 |
| 使用情境 | 明確的 API 或複雜的邏輯，需確保型別正確無誤時使用 | 簡單且容易推斷的區域變數或型別明顯時使用           |
| 補充     | 函式的參數必須要註記，不然會讓輸入參數是 any 型別 | 從變數名稱，上下文等可以很快看出型別，則可使用推論 |

TypeScript 是來補足 JavaScript 不夠嚴謹的部分，那麼如果站在嚴謹的角度，我們是不是乾脆 100% 使用註記呢？

雖然我們可以這麼做，但這樣會讓程式碼少了彈性，以及導致程式的可讀性變很差；關於註記跟推論的選擇，我們可以根據不同狀況，**考量程式可讀、維護性、TypeScript 型別的特性、團隊習慣等**，來判斷何時適當使用註記，而何時讓程式自己推論即可。而這個部分我覺得實際在團隊用 TypeScript 開發時就會越來越清楚了。

## 結語（感想）

寫這篇文章時，比預期花的時間還久了幾天，看到內容所累積的字數時也嚇了一跳。本來還想提到型別斷言的內容，但考量到篇幅以及手頭待整理的筆記，決定先將這個整理斷言內容的進度往後推。

如果沒記錯的話，好幾年前就買了<讓 TypeScript 成為你全端開發的 ACE！>這本書，但後來就都一直放在書架上；直到最近在學習 TypeScript 以及整理成文章時才開始認真翻閱，滿推薦想學 TypeScript 的開發者，可以來翻閱這本書，內容整理的很有條理跟易於了解。

最後想特別說，近期維持前端內容的輸出後，才深刻體會到這樣的知識輸出對於提升很有幫助。一方面是前端硬技能，另一方面則是將知識重新構建跟整理的軟技能，而後者這樣的軟技能，應該也適用在多個領域中。

參考資料：

- 六角學院 TypeScript 30 天課程
- 書籍：[<讓 TypeScript 成為你全端開發的 ACE！>](https://www.tenlong.com.tw/products/9789864344895?list_name=srh)

---

Photo by <a href="https://unsplash.com/@safarslife?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Safar Safarov</a> on <a href="https://unsplash.com/photos/turned-on-gray-laptop-computer-MSN8TFhJ0is?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
