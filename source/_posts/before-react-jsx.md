---
title: 寫在 React JSX 之前
date: 2024-10-30 14:25:59
tags: [React, JSX]
categories: [前端]
excerpt: Virtual DOM、React Element
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
---

## 前言

近期在前端工程的其中一個規劃是複習 React，也可以說是再度學習。原本這篇預計要筆記 JSX 的一些觀念跟需注意的部分，不過在找資料時看到 Zet 大大之前在 iThome 鐵人賽所撰寫的：[一次打破 React 常見的學習門檻與觀念誤解](https://ithelp.ithome.com.tw/users/20129300/ironman/5892)系列，裡頭就像寶庫一般，補足了我先前所缺的觀念。所以這一篇變成先筆記 Virtual DOM、React element、Render React elements 等所學觀念，而本來的 JSX 相關則挪動到下一篇。

題外話，在這樣的學習跟維持輸出，也讓我再意識到過去的習性跟方向，確實會讓自己的前端職涯不穩，也無法有效累積。而現在前端領域的變化之快，也不曉得自己後續的學習調整跟維持輸出習慣，能帶我走到什麼地方，但就繼續嘗試維持，然後看看再來的走向了！

## Virtual DOM

在先前學習 Vue 或 React 時，就滿常聽到 **Virtual DOM** 這個程式設計概念，不過那時並沒有特別去了解；藉由這次的學習規劃，來補足並整理相關的知識。

為什麼會有這樣的概念出現呢？首先要從**真實 DOM** 來談起。在瀏覽器中，真實 DOM 的操作都會直接影響到渲染引擎（rendering engine）的運行，因此若網站內容較為複雜、DOM 的操作也較為頻繁時，就會消耗瀏覽器的很多資源，進而有了效能上的問題；因此為了改善真實 DOM 所產生的效能問題，而衍生出 Virtual DOM 的概念。

Virtual DOM 是一種 JavaScript 物件結構，它不會直接跟瀏覽器的渲染機制產生互動。而按照其字面 Virtual 的意思，它是一種「**虛擬（模擬）**」的概念，用來先模擬真實 DOM 預計的樣子。每當有 UI 畫面需要更新時，整個流程大致如下：

1. **生成虛擬 DOM Tree**：依照我們所定義的需求，生成虛擬的 Virtual DOM Tree，會包含 Virtual DOM 元素的類型、屬性、子元素等資訊。
2. **比對變化（Diffing）**：會將此次新生成的 Virtual DOM Tree 跟前一次的 Virtual DOM Tree 進行比對，比對出新舊節點的差異部分。（差異部分就是會更新的地方）
3. **更新真實 DOM Tree**：根據上一個步驟的比對，將有差異的部分更新到真實 DOM Tree。

   ![Virtual DOM 流程](/img/before-react-jsx/1.png)

## React Element

在上一個段落中，我們了解到 Virtual DOM 這個程式設計概念。接下來要探討構成這個 Virtual DOM 的每個畫面元素 Virtual DOM element，而畫面元素在 React 被稱為 **React element。**

React element 是一種 JavaScript 物件資料，它用來描述（模擬）對應於真實 DOM 的節點資料跟結構，我們可以透過 React 提供的 `createElement()` 方法來建立一個 React element。如下方程式碼所示，`React.createElement()` 的三個參數分別為：元素類型、屬性、以及子元素等。

```jsx
import React from "react";

function App() {
	const helloReactElement = React.createElement(
		"div", // 元素類型
		null, // 屬性（這邊沒有屬性，所以為 null）
		React.createElement("h1", null, "Hello~"), // 子元素 1
		React.createElement("p", null, "About React Learn...") // 子元素 2
	);
	// console.log(helloReactElement);
	return helloReactElement;
}

export default App;
```

如果我們把上方範例的 React element 做 `console.log()`，我們會得到如下圖的物件資料。這個物件經過 React 處理後，就會轉換成真實的 DOM 元素並顯示在瀏覽器中。

![React element Object](/img/before-react-jsx/2.png)

`React.createElement()` 可以用來創建 React element，最終轉換為真實的 DOM 元素。不過，在開發中為了提高效率和可讀性，我們通常會使用 React 提供的語法糖 **JSX**，讓我們可以用**類似 HTML 的方式**來撰寫 React element。上方的範例改用 JSX 的寫法：

```jsx
function App() {
	return (
		<div>
			<h1>Hello~</h1>
			<p>About React Learn...</p>
		</div>
	);
}

export default App;
```

### 補充 - JSX 轉譯

在 React 的開發中，我們經常使用到 JSX 語法來撰寫畫面元素。然而，JSX 語法是需先經過**轉譯**才能被瀏覽器運行。而在負責轉譯的工具中，Babel 是其中比較主流，而且被 React 官方所推薦。

在 React 17 這個版本之前，JSX 相關語法會被 Babel 轉譯成 `React.createElement()`，而也因為這樣的轉譯需要引入 React 作為依賴，所以我們需要在程式最前方加上 `import React from 'react';` ，如下方範例程式：

```jsx
import React from 'react';

function App() {
	return (
		<div>
			<h1>Hello~</h1>
			<p>About React Learn...</p>
		</div>
	);
}

⬇️⬇️⬇️⬇️⬇️⬇️ // 轉譯

function App() {
  return React.createElement(
    "div",
    null,
		React.createElement("h1", null, "Hello~"),
    React.createElement("p", null, "About React Learn...")
  );
}
```

而在 React 17 版本之後，React 官方與 Babel 合作並支援了新的 JSX 轉譯方式，這樣的方式不再把 JSX 轉換成 `React.createElement()`，而是改成 `jsx-runtime` 的 `_jsx()` 方法。我們也不需要再 `import React` 作為依賴。關於這個部分的詳細，可參考[這篇文章](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html)。

上方的 JSX 範例，在現在的版本會被轉譯如下：

```jsx
function App() {
	return (
		<div>
			<h1>Hello~</h1>
			<p>About React Learn...</p>
		</div>
	);
}

⬇️⬇️⬇️⬇️⬇️⬇️ // 轉譯

import { jsx as _jsx, jsxs as _jsxs } from "react/jsx-runtime";

function App() {
  return _jsxs("div", {
    children: [
      _jsx("h1", {
        children: "Hello~"
      }),
      _jsx("p", {
        children: "About React Learn..."
      })
    ]
  });
}
```

關於 `_jsx()` 與 `React.createElement` 兩者的概念是類似的，只是 `_jsx()` 這樣的方法有再多一些優化。另外，在 [React 的中文文檔](https://zh-hans.react.dev/reference/react/createElement#creating-an-element-without-jsx)中有提到，如果開發者想用 JSX 以外的方式來建立 React element，還是只能使用 `React.createElement`。

### 補充 - React Element 的不可變性

## Render React elements
