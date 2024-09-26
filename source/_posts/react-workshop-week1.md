---
title: 六角學院 React 入門工作坊第一週
date: 2023-08-11
excerpt: 這篇文章主要是想把第一週作業的過程，做點紀錄跟刻意練習寫程式的思路
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
tags: [六角學院, React]
categories: [前端]
---

Photo by <a href="https://unsplash.com/@lautaroandreani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Lautaro Andreani</a> on <a href="https://unsplash.com/photos/xkBaqlcqeb4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

## 前言

因為身體關係，所以暫離了前端領域一段時間；而近期隨著身體已經趨於穩定（恢復中），所以想再度接回前端的軌道，目前保持進修中，也參加了六角開的 React 入門工作坊，增加與前端的連結。

先前寫過 Vue 一段時間，所以在學習 React 時，能比較知道這兩者的一些比對，React 寫法的確比較自由，但很吃原生 JS 的功力（這方面偏弱 QQ)，**而這篇文章主要是想把第一週作業的過程，做點紀錄跟刻意練習寫程式的思路**。

[作業放置處](https://codepen.io/yaj55ushydna/pen/dyQEjaP?editors=0110)

大致的流程為：

1. 定義資料並渲染
2. 調整庫存數量
3. 庫存數量優化
4. 編輯品項名稱

---

## 定義資料並渲染

不論使用哪個前端框架，我們都不是用 Dom 的角度做切入；而是採用**資料驅動畫面**的概念，所以定義資料、操作資料都是很常見的。

使用 JSX 語法上，需要習慣一段時間。分享我自己的小撇步：**在 JSX 語法中，如果要開始寫 JS 就用 {}；而準備要寫 HTML 就用 ()。**（註：這是一個記憶撇步，實際上 JSX 內的概念就都是 JS）

```js
const { useState } = React;

const drinkMenu = [
	// 略...
];

function MenuManage() {
	const [menu, setMenu] = useState(drinkMenu); // Hooks

	return (
		<div className="container">
			<h1>餐點管理工具</h1>
			<table>
				// ...略
				<tbody>
					{menu.map((item) => {
						return (
							<tr key={item.id}>
								<td>{item.name}</td>
								<td>
									<small>{item.content}</small>
								</td>
								<td>{item.price}</td>
								<td>{item.qty}</td>
							</tr>
						);
					})}
				</tbody>
			</table>
		</div>
	);
}

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<MenuManage />);
```

## 調整庫存數量

(考量到程式碼的長度，所以有些部分會做個省略)

再來我們要調整庫存數量，所以做了兩個按鈕來對應增減的功能，而像是 `() => handleQtyAdd(item.id) ` 所傳入的 `item.id` 則為識別用，判斷我們要改的是資料中的哪一個。

至於在庫存增加跟減少的功能中，我們得產出一組新的 array，然後再做 setState （下方範例是 setMenu），是因為 **React state immutable** 的設計概念。簡單來說就是寫 React 遇到要更新物件或陣列時，要根據更新需求去產新的物件或陣列。（有興趣的話可以 Google，或者參考這篇[文章](https://ithelp.ithome.com.tw/articles/10301603))。

```js
// 前略...

function MenuManage() {
	// Hooks 資料定義
	const [menu, setMenu] = useState(drinkMenu);

	// 庫存增加
	const handleQtyAdd = (id) => {
		// id 為傳過來的參數，用於判斷現在點到是第幾個
		const menuTemp = menu.map((item) => {
			return item.id === id ? { ...item, qty: (item.qty += 1) } : item;
		});
		setMenu(menuTemp);
	};

	// 庫存減少
	const handleQtyMinus = (id) => {
		const menuTemp = menu.map((item) => {
			return item.id === id ? { ...item, qty: (item.qty -= 1) } : item;
		});
		setMenu(menuTemp);
	};

	return (
		<div className="container">
			<h1>餐點管理工具</h1>
			<table>
				// 略...
				<tbody>
					{menu.map((item) => {
						return (
							<tr key={item.id}>
								// 略...
								<td>
									<button type="button" onClick={() => handleQtyMinus(item.id)}>
										-
									</button>
									{item.qty}
									<button type="button" onClick={() => handleQtyAdd(item.id)}>
										+
									</button>
								</td>
							</tr>
						);
					})}
				</tbody>
			</table>
		</div>
	);
}

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<MenuManage />);
```

如果不太理解三元運算子，或寫起來很卡的話，也可以先用最簡易的 if else 來寫，然後再慢慢對照三元運算的概念。
以下取庫存增加的 function 來做範例：

```js
// 庫存增加
const handleQtyAdd = (id) => {
	const menuTemp = menu.map((item) => {
		if (item.id === id) {
			return {
				...item,
				qty: (item.qty += 1),
			};
		} else {
			return item;
		}

		// return item.id === id
		//   ? { ...item, qty: (item.qty += 1) }
		//   : item;
	});
	setMenu(menuTemp);
};
```

## 庫存數量優化

因為庫存數量增減的 function 只有差異在 `item.qty += 1` 跟 `item.qty -= 1`，所以把增減的兩個 function 縮減成一個，然後多傳入一個參數（type) 作為增減的判斷。

```js
const { useState } = React;

const drinkMenu = [
	// 略 ...
];

function MenuManage() {
	// Hooks 資料定義
	const [menu, setMenu] = useState(drinkMenu);

	// 庫存數量功能
	const handleQtyFn = (id, type) => {
		const menuTemp = menu.map((item) => {
			return item.id === id
				? { ...item, qty: type === "add" ? (item.qty += 1) : (item.qty -= 1) }
				: item;
		});
		setMenu(menuTemp);
	};

	return (
		<div className="container">
			<h1>餐點管理工具</h1>
			<table>
				// 略 ...
				<tbody>
					{menu.map((item) => {
						return (
							<tr key={item.id}>
								// 略...
								<td>
									<button
										type="button"
										onClick={() => handleQtyFn(item.id, "minus")}
									>
										-
									</button>
									{item.qty}
									<button
										type="button"
										onClick={() => handleQtyFn(item.id, "add")}
									>
										+
									</button>
								</td>
							</tr>
						);
					})}
				</tbody>
			</table>
		</div>
	);
}

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<MenuManage />);
```

## 編輯品項名稱

這個部分剛開始有卡關一陣，主要是定義 state 資料所產生的問題。在編輯品項中，剛開始的粗淺想法是增加一個 boolen state 來做判斷 （像是：`const [status, setStatus] = useState(true)`），不過這樣的方式無法去識別**點編輯的項目是哪一個**，例如對某個品項點擊編輯，會發現全部品項都可以編譯。

後來改用定義 id（`const [currentId, setCurrentId] = useState('')`）的方式為依據。流程大致是：
在初始狀態 `currentId` 為空值時，每個餐點項目都是顯示"品項名稱"跟"編輯按鈕"；假設第二個品項中的編輯按鈕被點擊時，就會透過 `handleCurrentId` 以及第二個品項的 `item.id` 參數去做 `setCurrentId`，此時在樣板的 `item.id === currentId` 判斷中，第二個品項就會顯示 "input" 跟"完成按鈕"。

(至於 input 的 onChange，邏輯上跟前面庫存的寫法一致，所以就不再贅述。)

```js
const { useState } = React;

const drinkMenu = [
	// 略 ...
];

function MenuManage() {
	// Hooks 資料定義
	const [menu, setMenu] = useState(drinkMenu);
	const [currentId, setCurrentId] = useState("");

	// 略...

	const handleCurrentId = (id, type) => {
		type === "edit" ? setCurrentId(id) : setCurrentId("");
	};

	// input
	const handleChangeInput = (e, id) => {
		const menuTemp = menu.map((item) => {
			return item.id === id ? { ...item, name: e.target.value } : item;
		});
		setMenu(menuTemp);
	};

	return (
		<div className="container">
			<h1>餐點管理工具</h1>
			<table>
				// 略 ...
				<tbody>
					{menu.map((item) => {
						return (
							<tr key={item.id}>
								<td>
									{item.id === currentId ? (
										<>
											<input
												type="text"
												value={item.name}
												onChange={(e) => handleChangeInput(e, item.id)}
											/>
											<button
												type="button"
												onClick={() => handleCurrentId(item.id, "complete")}
											>
												完成
											</button>
										</>
									) : (
										<>
											{item.name}
											<button
												type="button"
												onClick={() => handleCurrentId(item.id, "edit")}
											>
												編輯
											</button>
										</>
									)}
								</td>
								// 略...
							</tr>
						);
					})}
				</tbody>
			</table>
		</div>
	);
}

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<MenuManage />);
```

---

## 結尾

實際在寫 React 的時候，還是能感受到自己原生 JS 功力比較弱，所以這樣的刻意練習，應該也是會有幫助的。另外如果有什麼部分或概念寫錯，也再麻煩指導，感謝！
