---
title: 寫在 React JSX 之前
date: 2024-11-05 15:41:50
tags: [React, JSX]
categories: [前端 React]
excerpt: 在 JSX 之前，先來了解 Virtual DOM、React element、Render React elements 等觀念。
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
---

## 前言

近期在前端工程的其中一個規劃是複習 React，也可以說是再度學習。原本這篇預計要筆記 JSX 的一些觀念跟需注意的部分，不過在找資料時看到 Zet 大大之前在 iThome 鐵人賽所撰寫的：[一次打破 React 常見的學習門檻與觀念誤解](https://ithelp.ithome.com.tw/users/20129300/ironman/5892)系列，裡頭就像寶庫一般，補足了我先前所缺的觀念。所以這一篇變成先筆記 Virtual DOM、React element、Render React elements 等所學觀念，而本來的 JSX 相關則挪動到下一篇。

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
（雖然我嘗試使用 \_jsx() 這樣的方式來建立 React element 並不會出錯，不過既然文件只寫到用 React.createElement，加上 JSX 語法糖一定是比較好撰寫的，所以就不調皮了 XD）

### 補充 - React Element 的不可變性

**React element 一旦建立後就不能再更改**，這與 Virtual DOM 的機制是相關的。

如前面的 Virtual DOM 段落所提，當畫面有更新需求時，我們會經過一個**比對變化（Diffing）**的階段，而這個過程會將新建立的 Virtual DOM 結構與舊有的（前一次）Virtual DOM 結構做比較，然後找出新舊結構的差異部分。所以如果先前的 Virtual DOM 結構被修改或覆蓋的話，這個機制就沒有一個依據（前一次）來跟新建立的 Virtual DOM 結構做比對了。

因此，React element 的不可變性意味著「**某個時間點的畫面狀態**」，所以當我們需要更新畫面（結構）時，就會以產生一組全新的結構（React element）來提供給 React，而不會去修改舊有的結構，以保證這個新舊的比對機制能夠成立。

## Render React elements

從定義抽象層到產生真實 DOM 元素（UI），React 將這整個過程分為 **Reconciler** 跟 **Renderer**。

- **Reconciler** 的任務是「抽象層的定義及畫面結構管理」，它會根據定義產生 React element 來模擬預期的 DOM 結構，而有畫面更新需求時，會比對新舊的結構差異，並將其結構差異交給 Renderer 處理。（也就是前述 Virtual DOM 流程中的生成虛擬 DOM、比對變化。）
- **Renderer** 的任務是「將畫面結構的模擬渲染成實際畫面（真實 DOM）」，它會將 Reconciler 所生成或更新（新舊結構比對差異）的 React elements，在目標環境（瀏覽器）中轉換成對應的實際畫面（渲染為真實 DOM）。

在瀏覽器的環境，我們需透過 `react-dom` 來將 React element 轉換並繪製成實際的 DOM。以下會以 vite 環境所建置出的 react 檔案來說明流程：

在 index.html 這支檔案中，我們會看到 `<div id="root"></div>` 區塊，這個區塊的用途是讓 Reconciler（Virtual DOM）到 Renderer 所產生的真實 DOM 可以注入於此。（id 的命名 `id="root"` 以便稍後在 JavaScript 可以取得這個元素）

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
	<!-- ...略 -->
	<body>
		<div id="root">
			<!-- React 輸出的真實 DOM elements 會注入到這裡 -->
		</div>
		<script type="module" src="/src/main.tsx"></script>
		<!-- 載入應用程式的入口檔案 -->
	</body>
</html>
```

接下來我們來看 main.tsx 專案進入點的檔案。

```jsx
// main.tsx 專案的進入點

import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client' // 用於瀏覽器環境，將 React element 轉換成真實的 DOM element
import App from './App.tsx'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

- `import { createRoot } from 'react-dom/client'`：從 `react-dom` 引入 `createRoot`，用途是將 React element 轉換成真實 DOM element。
- `import App from './App.tsx'`：匯入 `App` 元件，這個根元件會包含應用的其他組件，最終會被轉換成 React element。
- `createRoot(document.getElementById('root')!)`
  - `document.getElementById('root')!`：找到 HTML 定義好的 `id="root"` 區塊作為 React 渲染容器。
  - `createRoot()`：透過這個方法為 React 創建一個「根節點」，讓 React 能對這個根節點內做 DOM elements 的管理跟操作。
- `.render(<App />)`：將 `<App />` 元件（React element）插入到 `root` 節點，並渲染成真實的 DOM element。
- `StrictMode`補充：是 React 提供的一種開發模式，可用於檢測應用中不建議使用的 API 及找出潛在問題。這邊不會影響到 Render React elements 的了解，所以僅簡單說明。

### 補充 - React Fiber

在稍早的內容中，有提到從定義抽象層到產生真實 DOM 元素，React 將過程分為 Reconciler 跟 Renderer；而在 React 16 之後推出了 React Fiber 這個新的底層架構，所以在 Reconciler 跟 Renderer 之前，還多了「**Scheduler**」這個流程，它負責將 Reconciler 的工作碎片化，並進行一些分段、調度管理，讓畫面更新可以更順暢、彈性更高。

（參考相關文章：[React 開發者一定要知道的底層機制 — React Fiber Reconciler](https://medium.com/starbugs/react-%E9%96%8B%E7%99%BC%E8%80%85%E4%B8%80%E5%AE%9A%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84%E5%BA%95%E5%B1%A4%E6%9E%B6%E6%A7%8B-react-fiber-c3ccd3b047a1)）

![React Fiber](/img/before-react-jsx/3.png)

## 心得

整理這篇筆記所花的時間，比預期多了一些，因為有些觀念不太熟悉，所以需要重覆吸收跟整理。

題外話，這樣的學習跟維持輸出也一個月了，也讓我再意識到過去沒有專注跟打底，確實為自己帶來了前端職涯的不穩定。現在的前端領域變化相當快速，其實也不曉得後續這樣的輸出習慣能帶我走到什麼地方，但就繼續嘗試，然後再看看之後的走向哩！

參考資料：

[Zet - 一次打破 React 常見的學習門檻與觀念誤解](https://ithelp.ithome.com.tw/users/20129300/ironman/5892)

[Monica - [React] DOM, Virtual DOM 與 React element](https://medium.com/@linyawun031/react-dom-virtual-dom-與-react-element-af47110e2ec4)

[Introducing the New JSX Transform](https://legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html)

[React 中文文檔](https://zh-hans.react.dev/)

[React 開發者一定要知道的底層機制 — React Fiber Reconciler](https://medium.com/starbugs/react-%E9%96%8B%E7%99%BC%E8%80%85%E4%B8%80%E5%AE%9A%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84%E5%BA%95%E5%B1%A4%E6%9E%B6%E6%A7%8B-react-fiber-c3ccd3b047a1)