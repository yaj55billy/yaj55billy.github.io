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

......以 TypeScript 的漸進式型別系統總結一下。
總結來說，TypeScript 是一個漸進式型別系統（Gradually Typed System），它結合了靜態型別語言與動態型別語言的特性。開發者可以通過型別註記來指定變數型別，使其具備靜態型別的安全性；而同時 TypeScript 也可以利用型別推論，自動判斷變數的型別，讓開發者享有動態語言的靈活性。這種漸進式型別系統的設計，讓 TypeScript 可以適應不同的開發需求，提供兼具靈活與安全的編程體驗。

## 型別註記（Type Annotation）與型別推論（Type Inference）

儘管我們可以將所有的變數、函式、表達式等，主動且積極地註記出他們各自所代表的型別，但這並不一定表示你得這麼做。
根據不同情形，考慮：程式的可讀性、上手難度、維護難度、TypeScript 本身型別系統的特性，來判斷何時使用適當使用註記，而何時需讓程式自己推論...

如 100% 使用註記，則會導致程式碼可讀性變得很差。

### 型別斷言

## 結語（感想）

......

參考資料：

- 六角學院 TypeScript 30 天課程
- 書籍：[<讓 TypeScript 成為你全端開發的 ACE！>](https://www.tenlong.com.tw/products/9789864344895?list_name=srh)

---

Photo by <a href="https://unsplash.com/@safarslife?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Safar Safarov</a> on <a href="https://unsplash.com/photos/turned-on-gray-laptop-computer-MSN8TFhJ0is?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
