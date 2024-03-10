---
title: JavaScript 傳值與傳參考
date: 2023-10-26 10:51:17
tags:
  - JavaScript
  - 技術債
categories:
  - 前端
description: 關於 JavaScript 的傳值與傳參考特性
cover: https://i.imgur.com/DvyeScP.jpg
---

## JavaScript 的資料型別

在討論到傳值(Call by Value)或傳參考(Call by Reference)之前，我們先來了解 JavaScript 的資料型別，它分為「原始型別」跟「物件型別」兩大分類。

原始型別(純值)：
- Boolean
- String
- Number
- Null
- Undefined
- BigInt
- Symbol

物件型別：
除了以上七種原始型別之外，其他都歸類為物件型別。（所以並沒有陣列型別、函式型別）


## 傳值(Call by Value)、傳參考 (Call by Reference)

了解到 JavaScript 的資料型別後，若以一般、基本狀況來說，**原始型別的資料依循傳值的特性；而物件型別的資料則依循傳參考的特性**。

### 傳值(Call by Value)

```js
var num1 = 10;
var num2 = 10;
console.log(num1 === num2); // true
```
```js
var num1 = 10;
var num2 = num1;
num2 = 100;
console.log(num1 === num2); // false
console.log(num1, num2); // 10, 100
```

如上方兩個範例所示，如果你有一些些 JS 基礎，對於 `console.log` 的內容應該不會意外，我們以第二個範例作執行流程：
1. `var num1 = 10;`，宣告一個 num1 的變數，並指定為 10，這裡會建立一個記憶體(假設為0x01)來儲存 10 這個值。
2. `var num2 = num1;`，宣告一個 num2 的變數，並指定為 num1，這裡的 num2 會建立另一個記憶體(假設為0x02)，並將 num1 的值(10)複製過來儲存。
3. `num2 = 100;` 將 num2 的值改為 100。
4. `console.log(num1 === num2); console.log(num1, num2)` num2 的更動並不會影響到 num1，因為他們存在於兩個不同的記憶體位置，num1 的值為 10，而 num2 的值為 100。

**在原始型別的變數赋值和傳遞是以"值"來進行的，值跟值的關係是獨立不連動的。**


### 傳參考 (Call by Reference)

```js
var obj1 = {
  num: 10,
}
var obj2 = {
  num: 10,
}
console.log(obj1 === obj2); // false
```

物件在建立時，一樣會產生記憶體，不過這邊的變數儲存的是"記憶體位置"，而不是值（內容）。如上方範例所示，即使兩個物件內容是一致的，在`console.log`是得到不相等的，因為 obj1 跟 obj2 的記憶體參照位置不同。

```js
var obj1 = {
  num: 10,
}
var obj2 = obj1;
obj2.num = 100;
console.log(obj1.num, obj2.num); // 100, 100
console.log(obj1 === obj2); // true
```

我們再從這個範例拆解來更了解傳參考：
1. `var obj1 = { num: 10 }`，我們建立了一個內容為 `{num: 10}`的物件，而這個內容會儲存到記憶體(假設為 0x01)，而 obj1 這個變數是指向這個 0x01 的位置。
2. `var obj2 = obj1;`，接著我們將 obj1 給賦予到 obj2 變數，不過因為不是"創建"物件，所以 obj2 變數是指向到 0x01 的位置，也就是說 obj2 跟 obj1 都指向相同的記憶體位置。
3. `obj2.num = 100;`，雖然以變數名稱來說，似乎是針對 obj2 裡頭的內容做調整，但其實是調整到 0x01 這個記憶體位置所對應的內容。
4. `console.log(obj1.num, obj2.num);`，因為都是在 0x01 這個記憶體位置，所以兩者是相同且連動的。

**在物件型別中，變數的赋值和傳遞是以"記憶體位置"來做參照的。**

## 例外跟結論

```js
var obj1 = {
  num: 10,
}

function changeNum(param) {
  param.num = 100;
}

changeNum(obj1);
console.log(obj1); // {num: 100}
```

在上個段落的一開始提到，若以一般、基本狀況來說，原始型別的資料依循傳值的特性，而物件型別的資料則依循傳參考的特性。所以對於上方的範例結果，應該不會感到意外，即使作為 param 參數，它還是指向同個記憶體位置。

而有了一般狀況，就會有例外，接著我們來看例外的狀況是什麼。

```js

var obj1 = {
  num: 10,
}

function changeNum(param) {
  param = { num: 1000 }
}

changeNum(obj1);
console.log(obj1); // 仍然是 num: 10

```

如果依照剛才提到的傳參考，因為記憶體的指向，我們應該會推敲`console.log(obj1);`的結果是 `{ num: 1000 }`；不過實際上得到的結果是 `{num: 10}`，也就是參數(param)中指向新物件時，記憶體將會重新指向，而與原先的物件(obj1)將沒有關聯性。
所以主流上對於這個特性，比較大宗是以 Call by Sharing 這個名詞來做解釋，我自己在複習（~~補技術債~~）時也燒腦了一下；不過後來覺得不用太拘泥在這些名詞，而是知道 JS 的這些特性，盡可能避免踩雷。

## 參考資料

[0 陷阱！0 誤解！8 天重新認識 JavaScript！](https://www.tenlong.com.tw/products/9789864344130?list_name=srh)

[看完這本就會懂！帶你無痛提升 JavaScript 面試力](https://www.tenlong.com.tw/products/9786263332973?list_name=srh)

[深入探討 JavaScript 中的參數傳遞：call by value 還是 reference？](https://blog.huli.tw/2018/06/23/javascript-call-by-value-or-reference/)

[[筆記] 談談 JavaScript 中 by reference 和 by value 的重要觀念](https://pjchender.blogspot.com/2016/03/javascriptby-referenceby-value.html)

[JS基本觀念：call by value 還是reference 又或是 sharing?](https://medium.com/@mengchiang000/js%E5%9F%BA%E6%9C%AC%E8%A7%80%E5%BF%B5-call-by-value-%E9%82%84%E6%98%AFreference-%E5%8F%88%E6%88%96%E6%98%AF-sharing-22a87ca478fc)


