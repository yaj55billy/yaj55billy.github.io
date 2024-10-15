---
title: TypeScript 型別註記與型別推論
date: 2024-10-12 16:24:41
tags: [JavaScript, TypeScript]
categories: [前端 TypeScript 系列]
excerpt: 這篇主要探討 TypeScript 的核心，關於型別的註記跟推論
index_img: "/img/typescript.jpg"
banner_img: "/img/article-banner.jpg"
---

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

那麼 TypeScript 是屬於動態語言還是靜態語言呢？在這個系列的上一篇 [TypeScript 基本型別](https://yaj55billy.github.io/post/typescript-common-types.html)中，我們已經知道怎麼在變數加上型別宣告（如下範例），而這樣先以**文字來宣告其型別，並且會監控其型別狀態**的特性，可以知道 TypeScript 是有靜態語言的特性。

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

> 型別註記：為對變數或表達式進行文字敘述上的型別宣告動作
> 型別推論：則是變數根據被賦予的值之型別來代表該變數之型別；而表達式則是經運算結果的值之型別來代表整個表達式最後的型別結果。

接下來，我們將探討這兩者在不同情境下的使用，以及可能需要注意的部分。

### 原始型別的註記跟推論

這個段落主要會來探討原始型別的推論，而原始型別註記的相關內容，因為比較單純些，所以這邊就不再多提及。相關內容可參考 [TypeScript 基本型別 - 原始型別](https://yaj55billy.github.io/post/typescript-common-types.html#%E5%8E%9F%E5%A7%8B%E5%9E%8B%E5%88%A5%EF%BC%88Primitive-Types%EF%BC%89)。

如下範例所示，即使我們沒有註記上型別，TypeScript 也會根據被賦予的值來推斷出型別：

- 變數 text 會被推論為字串型別
- 變數 age 會被推論為數字型別
- 變數 isTrue 會被推論為布林型別
- 變數 money 會被推論為 null 型別
- 變數 notAssigned 會被推論為 undefined 型別

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

然而，如果變數在宣告時沒有被賦予任何值，TypeScript 的編譯器無法推論其型別，因此會用 `any` 型別來代替。`any` 型別意味著這個變數可以被賦予任何型別，而不會產生型別相關錯誤。這種情況稱為「遲滯性指派」。如下範例跟圖片所示：

```ts
let num;

// 以下指派都不會有錯誤，因為 any 型別
num = 123;
num = "字串";
num = true;
num = [1, 2, 3];
```

![遲滯性指派的 any 型別](/img/typescript-gradual-typing-2.png)

在某些情況下，TypeScript 會根據程式的判斷來推論變數的型別為聯合型別。聯合型別允許變數可以是多種型別中的一種。以下範例展示了這種情況：

```ts
let value;

if (Math.random() > 0.5) {
	value = "Hello";
} else {
	value = 42;
}

// TypeScript 會推論 value 的型別為 string | number
console.log(value);
```

在這個範例中，變數 value 的型別會根據程式的判斷來決定。因為 value 可能被賦予字串 "Hello" 或數字 42，所以 TypeScript 會推論 value 的型別為 string | number。

### 物件型別的註記跟推論

範例以及要注意什麼
看要不要提到 type 的用法？
分號與逗號的部分？
物件的完整性不允許被動搖

### 函式型別的註記跟推論

範例以及要注意的部分
函式型別絕大部分情形是 TypeScript 無法推論的...
參數建議一定要註記，畢竟沒有註記，會被 TypeScript 推論為 Any

### 陣列型別的註記跟推論

### 何時使用註記？何時使用推論？

儘管我們可以將所有的變數、函式、表達式等，主動且積極地註記出他們各自所代表的型別，但這並不一定表示你得這麼做。
根據不同情形，考慮：程式的可讀性、上手難度、維護難度、TypeScript 本身型別系統的特性，來判斷何時使用適當使用註記，而何時需讓程式自己推論...

如 100% 使用註記，則會導致程式碼可讀性變得很差。

註記：明確的 API 或複雜的邏輯，需確保型別正確無誤時使用。
推斷：對於簡單且易於推斷的區域變數，或當型別明顯時使用。

從變數名稱，上下文等可以很快看出型別。 > 使用推斷
函式的參數，會建議使用註記，不然等同於我們輸入的參數是 any 型別，容易延伸出一些意外的 Bug

以上這個部分是目前整理，加上一些個人的開發經驗，但我覺得這個部分，是寫 TypeScript 實際運行專案時，就會越來越清楚的部分。會判斷狀況，然後決定使用註記或推斷。

### 型別斷言

第二段最後說明斷言是什麼，與註記不同之處...

## 結語（感想）

自己在好久之前，就買了<讓 TypeScript 成為你全端開發的 ACE！>這本書，但一直都沒有真正翻閱，又或者翻閱卻腦袋一片空白，

但這次在認真研究 TypeScript 下，開始能看懂這些字詞...

......

參考資料：

- 六角學院 TypeScript 30 天課程
- 書籍：[<讓 TypeScript 成為你全端開發的 ACE！>](https://www.tenlong.com.tw/products/9789864344895?list_name=srh)

---

Photo by <a href="https://unsplash.com/@safarslife?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Safar Safarov</a> on <a href="https://unsplash.com/photos/turned-on-gray-laptop-computer-MSN8TFhJ0is?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
