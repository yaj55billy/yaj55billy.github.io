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
<img src="./image.jpg">
<input type="text" name="text">

<!-- 
	需要閉合的標籤遺漏了，雖然在 HTML 會被允許跟彌補；
	但容易導致意外的結構錯誤，需避免這樣寫哩
-->
<p>這是一段文字
<div>這是一個區塊
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

在 JSX 中，不同 JavaScript 的型別放入 `{}` 會有不同的渲染行為：

1. **能渲染到畫面上的型別**
   - 字串與數字：會直接顯示對應的文字或數字。
   - 陣列：會展開其中的項目並顯示內容。 
      - _這邊主要說明陣列型別放入 `{}` 的呈現方式，實際開發上不太會這樣呈現，而是使用 `.map()` 來生成內容_
2. **無法直接渲染的型別**
   - 布林值 (`true` / `false`)：不會渲染任何內容，若需要可搭配條件渲染。
   - `undefined` / `null`：不顯示任何內容（空白）。
   - 物件：整體無法直接渲染，會導致錯誤。
      - _需要顯示整體的物件結構時，可使用 `JSON.stringify(obj)`，偶爾在串接 API 時，會使用這樣的方式來看一下資料結構_

```jsx
function Example() {
	const ary = [1, 2, 3];
	const aryHtmlElement = [<li>項目一</li>, <li>項目二</li>];
	const obj = { name: "Billy" };

	return (
		<div>
			{/* 字串與數字 */}
			<p>字串：{"Hello, World!"}</p>
			<p>數字：{123}</p>

			{/* 陣列 */}
			<p>{ary}</p> {/* 顯示 "123"（沒有逗號） */}
			<ul>{aryHtmlElement}</ul> {/* 顯示 <li>項目一</li><li>項目二</li> */}

			{/* 布林值 */}
			<p>
				{true}
				{false}
			</p> {/* 不顯示任何內容 */}
			<p>{true ? "顯示內容" : "隱藏內容"}</p> {/* 顯示 "顯示內容" */}

			{/* undefined / null */}
			<p>{undefined}</p> {/* 空白 */}
			<p>{null}</p> {/* 空白 */}

			{/* 物件 */}
			<p>{obj}</p> {/* 錯誤：Objects are not valid as a React child */}
			<p>{obj.name}</p> {/* 顯示 "Billy" */}
			<p>{JSON.stringify(obj)}</p> {/* 顯示 '{"name":"Billy"}' */}
		</div>
	);
}
```

### **條件渲染與列表渲染**

在 React JSX 中，條件渲染與列表渲染是兩個常見的操作。條件渲染讓我們可以根據不同的條件來渲染不同的內容，而列表渲染則讓我們根據資料來動態生成多個元素。

**條件渲染：**

在下方這個範例中，我們根據 `isLoggedIn` 變數的值來判斷顯示的內容為「歡迎回來！」還是「請先登入。」

```jsx
function Example() {
  const isLoggedIn = true;
  return (
    <div>
      {isLoggedIn ? (
        <h1>歡迎回來！</h1>
      ) : (
        <h1>請先登入。</h1>
      )}
    </div>
  );
}
```

**列表渲染：**

在這個範例中，我們使用 `map()` 方法來迭代 `items` 陣列（資料），並為每個項目生成一個 `li` 元素。

需注意在列表渲染生成的每個元素，需要有一個**唯一的 `key` 屬性。**`key` 是用來幫助 React 進行元素的**識別**跟追蹤，當列表中的項目新增、移除或重新排序時，`Key` 會告訴 React 每個元素對應的是哪個資料項目，讓 React 能正確地匹配和更新元素。

```jsx
function Example() {
  const items = [
    { id: 1, name: '衣物' },
    { id: 2, name: '電腦' },
    { id: 3, name: '3C 產品充電線' }
  ];

  return (
    <div>
      <h1>我的行李清單：</h1>
      <ul>
        {items.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
      
      {/* 列表渲染後如下 ⬇️ ⬇️ ⬇️ ⬇️ ⬇️ ⬇️ */} 
      <ul>
        <li>衣物</li>
        <li>電腦</li>
        <li>3C 產品充電線</li>
      </ul>
    </div>
  );
}
```

以上兩個範例，主要展示了條件渲染和列表渲染的基本使用，而在實際開發中，這些寫法將會是很常使用的！

### 補充：關於 JSX `{}` 放入陳述式函式

從先前內容，我們可以知道 JSX 的大括號 `{}` 中只能放入**表達式**，而不能放入**陳述式**；不過函式就比較特別，即使我們在 `{}` 放入陳述式函式 `{ function(){} }` 也不會出錯，因為它在編譯之後，是**作為參數**傳入 `React.createElement()` ，就像 Callback function 一樣。

編譯範例：

```jsx
function App() {
	return (
		<div>
			<h1>Hello~</h1>
	     {
	       function(){
	         return 2*2;
	       }
	     }
		</div>
	);
}

function App() {
  return React.createElement(
    "div",
    null,
    React.createElement("h1", null, "Hello~"),
    function () {
      return 2 * 2;
    }
  );
}
```

會特別紀錄這一段，是因為先前自己對這個概念，腦袋是有些轉不過來的，不過後來看了編譯結果後就有比較了解。