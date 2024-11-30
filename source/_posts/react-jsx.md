---
title: React JSX
date: 2024-11-25 21:04:47
tags: [React, JSX]
categories: [前端 React]
excerpt: React JSX
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
---

## 前言

在「[寫在 React JSX 之前](https://yaj55billy.github.io/post/before-react-jsx.html)」這篇文章中，我們已經有探討到 React element 以及 Render 等相關概念，內容也有提到：`React.createElement()` 可以用來創建 React element，最終轉換為真實的 DOM 元素，不過為了開發的效率跟可讀性，我們通常會使用 React 提供的語法糖 JSX。

這篇文章將接續來說 JSX。

## JSX 是什麼

JSX，全名為 **JavaScript XML**，是 React 提供的語法糖。它讓我們在撰寫 UI 結構時有**類似** HTML 的語法體驗，而**實際上 JSX 的本質是 `React.createElement()` 方法的呼叫**，並非是在 JavaScript 中撰寫 HTML。

在以下範例中，分別使用了 `React.createElement()` 和 JSX 語法來建構相同的 UI 結構。雖然程式碼的寫法不同，但經過編譯後，兩者會產生相同的結果：

```jsx
// 使用 React.createElement()
function App() {
	return React.createElement(
		"div",
		{ id: "wrapper" },
		React.createElement("h1", null, "Hello, JSX!")
	);
}

// 使用 JSX
function AppWithJSX() {
	return (
		<div id="wrapper">
			<h1>Hello, JSX!</h1>
		</div>
	);
}
```

雖然我們可以使用 `React.createElement()` 方法來撰寫 React App，但這種方式的程式碼相對冗長且不易閱讀與維護；因此，在實際開發時通常採用 JSX 語法，以類似 HTML 的方法來定義畫面，不僅使程式碼的可讀性更好，也提升了開發者的體驗跟效率。

從「寫在 React JSX 之前」的 [JSX-轉譯](https://yaj55billy.github.io/post/before-react-jsx.html#補充-JSX-轉譯)段落中，我們可以得知 JSX 是無法直接被瀏覽器執行的，而是需透過**編譯工具**將其轉換為 JavaScript（`React.createElement()`）才能運作。而為了確保這個轉換過程的正確性，所以 JSX 語法在撰寫時會有一些特定的規則。

接下來將分段來討論 JSX 的語法特性。

## 嚴格的標籤閉合

在撰寫 HTML 時，瀏覽器具有高度的容錯性。即使我們遺漏了標籤的閉合，瀏覽器仍能根據內容推測並重建 DOM 結構。例如，空元素（像是 `<img>` 和 `<input>`）即使未顯式加上閉合符號 `/`，瀏覽器依舊可以正確處理；而對於需要閉合的區塊元素（如 `<p>` 和 `<div>`），如果遺漏了閉合，瀏覽器也會嘗試彌補結構缺失。

```html
<!-- 空元素沒有加上閉合符號 OK -->
<img src="./image.jpg" />
<input type="text" name="text" />

<!-- 
	需要閉合的標籤遺漏了，雖然在 HTML 會被允許跟彌補；
	但容易導致意外的結構錯誤，需避免這樣寫哩
-->
<p>這是一段文字</p>
<div>這是一個區塊</div>
```

然而，**在 JSX 的撰寫上，對於標籤的閉合有更為嚴格的要求**。例如，在 HTML 中，空元素（如 `<img>` 和 `<input>`）即使未加上閉合符號也能正常運作；但在 JSX 中，這些標籤必須以自閉合的形式呈現（如 `<img />` 或 `<input />`）。

如果標籤沒有正確閉合，**編譯工具（JSX transformer）將無法判斷 React element 的結束位置**，最終導致轉換的錯誤。

以下是 JSX 的正確與錯誤寫法範例：

```jsx
// 正確的 JSX 寫法 ✅
function App() {
  return (
    <div>
      <img src="./image.jpg" alt="example" />
      <input type="text" name="text" />
      <p>這是一段文字</p>
    </div>
  );
}

// 錯誤的 JSX 寫法（未閉合標籤）❌
function AppWithErrors() {
  return (
    <div>
      <img src="./image.jpg">
      <input type="text" name="text">
      <p>這是一段文字
    </div>
  );
}
```

## 屬性名稱

在 React JSX 的屬性中，我們大部分會看到駝峰式這樣的命名（camelCase），這是因為 JSX 是 JavaScript 的語法擴展，所以在屬性命名中需遵循 JavaScript 的特性跟變數命名規範，像是變數名稱不能使用連字符號 `-` ，或者在一些保留字上會有特定的替代詞（例如： `class` 改為 `className`）。

在語言特性跟設計考量，JSX 在屬性名稱的寫法上，就會與 HTML 有所差異，以下將列出幾個常見的屬性名稱轉換。

**`className`**

```jsx
// HTML
<div class="container"></div>

// JSX
<div className="container"></div>
```

**`htmlFor`**

```jsx
// HTML
<label for="username">Username</label>

// JSX
<label htmlFor="username">Username</label>
```

`onClick`

```jsx
// HTML
<button onclick="handleClick()">Click Me</button>

// JSX
<button onClick={handleClick}>Click Me</button>
```

**表單預設值屬性 `defaultValue`、`defaultChecked`**

```jsx
// HTML
<input type="text" value="Hello" />
<input type="checkbox" checked />
<select>
    <option value="option1">Option 1</option>
    <option value="option2" selected>Option 2</option>
    <option value="option3">Option 3</option>
</select>

// JSX
<input type="text" defaultValue="Hello" />
<input type="checkbox" defaultChecked />
<select defaultValue="option2">
  <option value="option1">Option 1</option>
  <option value="option2">Option 2</option>
  <option value="option3">Option 3</option>
</select>
```

**樣式屬性（物件格式）**

```jsx
// HTML
<div style="background-color: blue; font-size: 16px;"></div>

// JSX：style 屬性需要以 JavaScript 物件形式傳遞，並且 CSS 屬性名稱需要改為駝峰式命名
<div style={{ backgroundColor: 'blue', fontSize: '16px' }}></div>

```

**例外狀況 `aria-*`、`data-*`**

```jsx
// HTML
<div aria-label="description" data-id="123"></div>

// JSX
<div aria-label="description" data-id="123"></div>
```

如果初學 JSX 還不太熟悉，也可以先使用這樣的工具：[html-to-jsx](https://transform.tools/html-to-jsx)

## 表達式 `{}`

JSX 讓我們可以使用類似 HTML 的語法來定義 React element，而我們也可以透過大括號 `{}` 這樣的方式，將 JavaScript 的邏輯與動態內容嵌入其中，例如表達式、變數、函式呼叫或三元運算等。

需注意：`{}` 中只能放入**表達式**，不能放入**陳述式**（如 `if-else`）

下方範例中，先簡單展示了如何使用 `{}` 在 JSX 中動態放入 JavaScript 表達式，像是變數、函式呼叫和三元運算判斷。

```jsx
function Example() {
	// 宣告變數
	const userName = "Billy";
	const isLoggedIn = true;
	const getGreeting = (name) => `Hello, ${name}!`;

	return (
		<div>
			{/* 在內容中使用變數與函數呼叫 */}
			<h1>{getGreeting(userName)}</h1>

			{/* 在屬性中使用變數 */}
			<img
				src="https://via.placeholder.com/150"
				alt={`Avatar of ${userName}`}
			/>

			{/* 使用三元運算式進行條件渲染 */}
			<p>{isLoggedIn ? "You are logged in." : "Please log in."}</p>
		</div>
	);
}

export default Example;
```

### 表達式與 JavaScript 型別

在 JSX 中，不同 JavaScript 的型別放入 `{}` 會有不同的渲染行為，有些型別能直接呈現於畫面中，而有些型別則不會呈現，甚至會報錯。

首先，我們來看不會呈現於畫面的 JavaScript 型別，像是**物件**、原始型別的**布林值**、**undefined、null**。

```jsx
function Example() {
	const obj = { name: "Billy" };

	return (
		<div>
			{/* 布林值不會直接被渲染，如果有需要渲染，可使用三元運算的方式 */}
			<p>
				{true}
				{false}
			</p>
			<p>{true ? "true" : "false"}</p>
			{/* 
        ------ 分隔線 ------
      */}
			{/* undefined、null 也不會被渲染 */}
			<p>{undefined}</p>
			<p>{null}</p>
			{/* 
        ------ 分隔線 ------
      */}
			{/* 物件 */}
			<p>{obj}</p> {/* 整體不能直接被渲染 */}
			<p>{obj.name}</p> {/* 渲染物件中的屬性是 OK 且常見的 */}
			{/* 
		      如果要渲染出物件整體結構，
		      可用 JSON.stringify() 的方式。
		      實際串接 API 時，有時會用這樣來看一下資料
		    */}
			<p>{JSON.stringify(obj)}</p>
		</div>
	);
}
```

而像**陣列**與原始型別中的**字串**、**數字**是能渲染於畫面之中。

```jsx
function Example2() {
	const ary = [1, 2, 3];
	const aryHtmlElement = [<li>項目一</li>, <li>項目二</li>];

	return (
		<div>
			{/* 字串與數字能直接渲染到畫面 */}
			<p>字串：{"Hello, World!"}</p>
			<p>數字：{123}</p>

			{/* 
	      陣列會展開其中的項目於畫面中（排除逗號），
	      這個範例就會在畫面呈現出 123
	    */}
			<div>{test}</div>

			{/* 
		      甚至能渲染出 HTML Element， 
		      <li>項目一</li>
		      <li>項目二</li>
		      
		      但實戰中不太會這樣用
		    */}
			<ul>{aryHtmlElement}</ul>
		</div>
	);
}
```
