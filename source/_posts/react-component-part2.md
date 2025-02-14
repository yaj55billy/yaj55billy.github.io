---
title: React component（元件）- 下篇
date: 2025-01-22 14:48:01
tags: [React, Component]
categories: [前端 React]
excerpt: 下篇...
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
hide: true
---

## Props 是唯讀不能被修改的

在 [React component（上篇）](https://yaj55billy.github.io/post/react-component-part1.html)，了解到 component 以及 props 的定義跟操作後，接下來要看 React 對於 props 是唯讀的設計（限制）。

當我們從父層 component 將資料以 props 的形式傳遞給子層 component 之後，如果子層 component 能隨意修改父層傳入的 props，那麼資料源頭將會是不可控的，或者經過多次修改後變得混亂而難以追蹤。

所以為了維護跟保證資料的源頭是唯一不變、可追蹤的狀況，在開發環境的 React 會把 props 物件以 `Object.freeze(props)` 給凍結起來，來實現 props 是唯讀的設計。

如下方一個簡單範例，來說明這樣的機制：

當子層 UserProfile component 想透過 tryToChangeName 來修改 props.name 時，將不會有任何的改動發生，而且在 console 會出現：`Cannot assign to read only property 'name'` 的錯誤。

```jsx
// 子層 component
const UserProfile = (props) => {
	// ❌ 錯誤：直接修改 props
	const tryToChangeName = () => {
		props.name = "小華"; // 這會導致錯誤！Cannot assign to read only property 'name'
	};

	// ✅ 單純讀取 props 的值
	return (
		<div>
			<p>姓名：{props.name}</p>
			<p>年齡：{props.age}</p>
			<button onClick={tryToChangeName}>試著修改姓名</button>
		</div>
	);
};

// 父層 component
const App = () => {
	return (
		<div className="container">
			<h1>個人資料</h1>
			<UserProfile name="小明" age={25} />
		</div>
	);
};
```

透過這樣的機制，React 確保了子層 component 只能讀取而不能修改父層傳入的 props，維護了資料流向的單一和可控制性。

[此範例的 StackBlitz 連結](https://stackblitz.com/edit/vitejs-vite-8btyyswl?file=src%2FApp.jsx)

### 例外狀況 - 傳參考

## Component 的 render

## 為何 component 的命名首字母需要為大寫
