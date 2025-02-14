---
title: React useContext 跨元件狀態
date: 2025-02-14 15:00:31
tags: [React, useContext]
categories: [前端 React]
excerpt: useContext 是一個用於共享狀態和方法的機制，而這樣的機制可以把狀態和方法直接傳遞給需要的元件，而不必透過元件層層傳遞。
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
---

## Prop Drilling

在撰寫 React 時，通常會使用 props 來做不同層級元件的資料傳遞；不過在層級較多與複雜時，甚至會有些中間層元件只是協助接收跟傳遞 props，而它們本身並不使用這些 props 狀態，使得這些中間層元件的職責混亂。而這樣的多層傳遞，會導致程式碼變的較難維護，像是程式碼有更動時，還需注意這些中間層元件是否有漏傳 props。這樣的狀況被稱為 "Prop Drilling"。

## useContext：擺脫層層傳遞

關於 Prop Drilling，這時我們可以使用 React 提供的 useContext，它是一個用於共享狀態和方法的機制，而這樣的機制可以把狀態和方法直接傳遞給需要的元件，而不必透過元件層層傳遞。

如下圖所示，在右圖是使用一般 props 層層傳遞，而左圖中的虛線表示使用 useContext 獲取資料的路徑，這些元件可以直接取得 Context 中的資料，而不需要透過元件層層傳遞。
![使用一般 props 跟 useContext 差異](img/react-usecontext/1.webp)

## 使用 useContext 來實作購物車功能（範例）

在網站開發中，購物車功能是很常見的需求。而它的資料和功能也很常在不同元件間使用，像是：

- 商品列表需要「加入購物車」功能
- 購物車明細需要顯示商品明細、金額，以及「移除購物車品項」功能
- 導航列（Header）可能會需要顯示購物車數量
- ……

接著我們以一個簡化的購物車功能來練習使用 useContext。結構如下：

```
App
├── CartProvider（Context 提供者）
├── ProductList（商品列表）
│   └── 加入購物車功能
└── CartSummary（購物車明細）
    ├── 商品列表
    ├── 總金額顯示
    └── 移除購物車品項功能
```

### 建立 Context 和 Provider

程式碼範例（CartContext.jsx）：

```jsx
import { createContext, useState } from "react";

// 建立 Context
const CartContext = createContext({});

// 建立 Provider，封裝共用的狀態和方法
export const CartProvider = ({ children }) => {
	const [items, setItems] = useState([]);
	const [totalAmount, setTotalAmount] = useState(0);

	const addToCart = (product) => {
		setItems((prev) => [...prev, product]);
		setTotalAmount((prev) => prev + product.price);
	};

	const removeFromCart = (productId) => {
		const product = items.find((item) => item.id === productId);
		setItems((prev) => prev.filter((item) => item.id !== productId));
		setTotalAmount((prev) => prev - product.price);
	};

	// 提供要共用的資料和方法
	return (
		<CartContext.Provider
			value={{
				items,
				totalAmount,
				addToCart,
				removeFromCart,
			}}
		>
			{children}
		</CartContext.Provider>
	);
};

export default CartContext;
```

- 建立 CartContext.jsx，並引入 createContext、useState 等方法。createContext 概念為建立一個共享空間來準備存放資料，這時空間還沒有具體內容。
- 建立 CartProvider，概念就像正式開放這個空間，並提供裡面的內容，讓需要的組件可以存取和使用。
  - 購物車商品列表（items）
  - 總金額（totalAmount）
  - 操作購物車的方法（addToCart、removeFromCart）
- 在 CartContext.Provider 的 value 為定義要共享出去的資料跟方法，而 children 為之後所包覆的子元件

### 使用 Provider 包覆需要的元件

程式碼範例（App.jsx）：

```jsx
// App.jsx
import { CartProvider } from "./CartContext";
import ProductList from "./components/ProductList";
import CartSummary from "./components/CartSummary";

function App() {
	return (
		<CartProvider>
			<div>
				<h1>簡易購物車示範</h1>
				<ProductList />
				<CartSummary />
			</div>
		</CartProvider>
	);
}
```

- 從 CartContext 元件中引入 CartProvider，並包覆需要使用這個 Context 資料和方法的元件。
  - ProductList、CartSummary 等元件，都能使用 CartContext 裡頭定義的資料跟方法

### 在元件中使用 Context

接著我們就可以在 ProductList、CartSummary 等元件，使用 useContext 來從 CartContext 取得共用的資料和方法。

```jsx
// CartSummary.jsx 購物車元件
import { useContext } from "react";
import CartContext from "../CartContext";

const CartSummary = () => {
	const { items, totalAmount, removeFromCart } = useContext(CartContext);

	return (
		<div>
			<h2>購物車明細</h2>
			{items.map((item, index) => (
				<div key={index} style={{ marginBottom: "10px" }}>
					<span>
						{item.name} - ${item.price}
					</span>
					<button
						onClick={() => removeFromCart(item.id)}
						style={{ marginLeft: "10px" }}
					>
						移除
					</button>
				</div>
			))}
			<div style={{ marginTop: "20px" }}>
				<strong>總金額: ${totalAmount}</strong>
			</div>
		</div>
	);
};

export default CartSummary;
```

這邊只先呈現 CartSummary 元件。[完整範例連結](https://stackblitz.com/edit/vitejs-vite-km71hap3?file=src%2FCartContext.jsx)

總結用法：

- 1️⃣ **createContext → 創建一個共享空間**
- 2️⃣ **Provider → 把共享內容（資料和方法）放進去，讓大家可以使用**
- 3️⃣ **useContext → 在有被 Provider 包覆的元件中，可取用這個 Context 空間的資料跟方法**

## 注意事項

雖然 useContext 是滿方便的，但仍需根據需求而謹慎使用。列出一些注意項目：

- **適度使用**：並非所有狀態都納入 Context，在元件層傳遞不太複雜時，或狀態只在特定的元件使用時，那麼建議優先使用 props 或 useState 來管理。（[官方文件](https://react.dev/learn/passing-data-deeply-with-context#before-you-use-context)）
- **重新渲染**：當 Context 內的狀態變更時，所有使用該 Context 的元件都會被重新渲染。對於高頻率變更，可依據狀況考量在元件使用 useMemo、Memo，或者使用其他狀態管理工具，例如 Redux。
- **適當拆分：**若有需要使用 Context，則可根據不同功能來做拆分，避免所有狀態都集中在一個 Context 內，這樣可以減少不必要的重新渲染，也讓程式碼更好維護。

—

參考資料：

- https://react.dev/learn/passing-data-deeply-with-context#before-you-use-context

- https://ithelp.ithome.com.tw/m/articles/10296652
