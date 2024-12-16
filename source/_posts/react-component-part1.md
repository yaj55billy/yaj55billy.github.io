---
title: React component（元件）- 上篇
date: 2024-12-11 16:39:04
tags: [React, Component]
categories: [前端 React]
excerpt: Component（元件）是一種開發者根據需求所自定義的畫面模組，這個模組負責呈現特定範圍的畫面內容或邏輯，是可以重複使用的程式碼。
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
---


## 什麼是 component（元件）？

**Component（元件）是一種開發者根據需求所自定義的畫面模組，這個模組負責呈現特定範圍的畫面內容或邏輯，是可以重複使用的程式碼。**

以這個 Blog 網站為例（如下圖所示），像是 Header、文章列表 ArticleList、單篇文章 ArticleListItem、分頁 Pagination 等不同的區塊，都可根據需求而拆解成 component。這些不同的 component 可以組合出更大的 component 或頁面，也能被重覆使用，像是在其他頁面中需要換頁時，我們就能引入分頁 component 作為使用。而這樣可以重覆使用跟組合的特性，就如同在玩樂高積木一般。

![Blog 網站作為元件示例](/img/react-component-part1/1.png)

## 定義與呼叫 component

### 定義 component

在 React 的開發中，我們使用 JavaScript 函式來定義 component。這些函式會回傳 JSX 語法，最終被轉譯成 React element 作為畫面結構。

而定義 **component 名稱的首字母必須是大寫**，像是 `Article` 或 `ArticleList` ，這樣除了是 React 在開發的命名習慣，也跟 JSX 語法的轉譯有關，這個部分將會在 React component（下篇）來進一步說明。

另外一提，目前 React 開發的主流，主要是採用 **function component** 搭配 **hooks** 的方式，因此本篇不會討論 class component 的寫法。

定義範例：

```jsx
function Article() {
  return (
    <div>
      <h2>文章標題</h2>
      <p>文章內容......</p>
    </div>
  )
}
```

### 呼叫 component

了解如何定義 React component 後，讓我們來看看如何呼叫（使用）component。

在 React 中，我們使用**標籤語法**來呼叫自定義的 component，例如 `<Article />`。當這個標籤被繪製時，就會執行對應的 function component，並將其回傳的 JSX 結構插入到標籤呼叫 component 的位置上。

兩種呼叫 component 的方式：

- **單標籤結尾：**`<Article />`
- **開頭與結尾標籤**：`<Article></Article>`

```jsx
function Article() {
  return (
    <div>
      <h2>文章標題</h2>
      <p>文章內容......</p>
    </div>
  )
}

function App() { 
  return (
    <>
      <h1>首頁</h1>
      <p>首頁內容...</p>
      {/* 呼叫 Article component */}
      <Article />
      <Article></Article>
    </>
  )
}
```

在上方範例中，我們定義了一個 `Article` component，並在 `App` component 中呼叫它兩次。而在繪製並執行後，`Article` component 會回傳 JSX 結構（`<div><h2>文章標題</h2><p>文章內容......</p></div>`）並插入到 `<Article />` 標籤所在的位置。

### 關於 import、export

在現代開發習慣中，使用像 **Vite** 這樣的腳手架工具來建立 React 專案已相當普遍。這類工具大幅縮短了專案初始的配置時間，也預設了**模組化開發**的環境，讓開發流程更加有效率。

基於模組化的概念跟環境，我們通常會將每個 component 定義在獨立的檔案中，而不是將所有 component 集中在同一支檔案內。這樣的方式讓我們更方便進行 component 的重用跟管理。

而為了讓這些 component 能夠在不同檔案間共享，我們會使用 **ES module** 語法來匯出（export）與匯入（import）。而要將 component 匯出時，有兩種主要的方式：`default export`（預設匯出） 跟 `named export`（具名匯出）。在一個 JavaScript 的檔案中，只能有一個 `default export`，但可以同時有多個 `named export`。

**`default export` 預設匯出**

通常用於匯出單一**主要 component。**根據命名習慣，檔案名稱通常會與 function component 的名稱保持一致。例如，若有一個文章內容的 component，其檔案命名為 `Article.jsx`，則該 component 的名稱也會是 `Article`。

如下範例展示 `default export` 跟 `import`：

```jsx
// Article.jsx
function Article() {
	return <div>這是一篇文章</div>
}
export default Article;

// 也可以改寫為：
export default function Article() {
	return <div>這是一篇文章</div>
}
```

```jsx
// App.jsx（或其他檔案）
import Article from './Article'; // import Article component

const App = () => {
  return (
    <div>
      <Article />
    </div>
  )
}

export default App
```

**`named export` 具名匯出**

適用於同一檔案中需要匯出多個功能或 component 的情況，匯入時需要使用大括號指定名稱。

如下範例展示 `named export` 跟 `import`:

```jsx
// Layout.jsx

export function Header() {
  return <header>這是頁首</header>;
}

export function Footer() {
  return <footer>這是頁尾</footer>;
}
```

```jsx
// 在其他檔案中使用
import { Header, Footer } from './Layout'; // import

function App() {
  return (
    <div>
      <Header />
      <Footer />
    </div>
  )
}

export default App;
```

在這個範例中，`Layout.jsx` 定義了兩個元件 `Header` 和 `Footer`，並透過 `named export` 匯出。而其他檔案在匯入時，必須使用大括號指定需要的元件名稱。

## Props

在說明 Props 之前，先以西式餐廳作為比喻。假設我們在菜單上點了一份義大利麵，這份義大利麵本身有其固定的基本組成，例如：麵條、醬汁與基本配料等，這些基本組成就像 **Component 的預設結構與內容**。然而，這道義大利麵也具備一定的彈性，可以根據客人的需求進行客製化調整，例如：少鹽、加麵、不要辣等，這些額外的客製化需求，就像是 **Component 的 Props**，它讓義大利麵在固定的架構下能根據不同的需求，做出一些額外的變動，進而滿足每位客人的需求。

有了這樣的比喻後，我們回到 React props 的說明。Props 是 component 的「properties（屬性）」，它可用在 component 的資料傳遞。當我們在呼叫 component 並建立 React element 時，可以將特定的參數從外部傳遞到 component 之中，進而讓這個 component 根據這些參數來產生不同內容或邏輯，這讓 component 更加靈活並適應於不同情境。

接下來，我們來看在實際的程式碼中使用 props：

### 呼叫傳入 props

如下程式碼範例，在 `App` 這個 component 中，我們呼叫了兩次 `Article` component，並且每次都傳遞了 `title` 和 `content` 這兩個 props：

```jsx
const App = () => {
  return (
    <div>
      {/* 傳遞 props */}
      <Article title="第一篇文章" content="這是文章內容一" />
      <Article title="第二篇文章" content="文章內容二..." />
    </div>
  );
};
```

透過這樣的方式，我們可以將不同的資料傳遞到 `Article` component，讓它根據這些傳入的值產生不同的畫面內容。例如，第一篇文章與第二篇文章的標題和內容，會根據我們指定的值而呈現出各自的差異。

**補充：** 每個**元件擁有自己的狀態和邏輯**，因此是不會互相影響的。在上述範例中，即使多次呼叫 `Article` component，它們仍是彼此獨立的，彼此的資料變更不會互相干擾。

### 接收 props

當我們傳遞了 props 給某個 component 時，這個 component 可以在內部接收這些 props，並依據它們來產生對應的畫面內容或執行邏輯。例如，以下是 `Article` component 的兩種接收 props 的方式：

```jsx
// 方式一：直接使用 props 物件
const Article = (props) => {
  return (
    <div>
      <h2>{props.title}</h2>
      <p>{props.content}</p>
    </div>
  );
};

// 方式二：透過解構語法提取 props
const Article = ({ title, content }) => {
  return (
    <div>
      <h2>{title}</h2>
      <p>{content}</p>
    </div>
  );
};
```

- **方式一**：直接使用 `props`，透過 `props.title` 和 `props.content` 來存取資料。
- **方式二**：使用解構語法，透過 `{}` 提取出 `title` 和 `content`，使程式碼更為簡潔。

當我們傳遞資料到 `Article` component 時，它能根據這些資料顯示對應的內容。例如，`title` 和 `content` 的值會分別渲染成標題與段落，讓每個 `Article` 擁有自己的顯示內容。

## 結語

這次關於元件的筆記整理，主要是來自 Zet 大的書籍 <React 思維進化>，加上自己的一些消化，透過這樣的整理，也幫助自己釐清先前寫元件時沒有注意到的知識點。

目前是 component 上篇，下篇預計要筆記的內容：

- Props 是唯讀不能被修改的
- Component 的 render
- 為何 component 的命名首字母需要為大寫

------

參考資料：

[Zet - React 思維進化](https://www.tenlong.com.tw/products/9786263336841)

[六角學院 - React 實戰影音](https://www.hexschool.com/courses/react.html)