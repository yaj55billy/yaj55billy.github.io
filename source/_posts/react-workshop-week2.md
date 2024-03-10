---
title: 六角學院 React 入門工作坊第二週
date: 2023-08-19
tags:
  - 六角學院 
  - React
categories:
  - 前端
description: 第二週作業的過程
cover: https://i.imgur.com/Zwb2dxR.jpg
---

Photo by <a href="https://unsplash.com/@lautaroandreani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Lautaro Andreani</a> on <a href="https://unsplash.com/photos/xkBaqlcqeb4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

## 前言

本週心得小提：
課程除了可以學習到 React 操作，還可以從老師怎麼拆解問題，以及同學的程式碼中（有些同學感覺是有經驗的開發者），去看到不同的思路，擴展出解決問題的眼界。

記得這週課程快結尾時，老師有提到工程師的差異，厲害的工程師是會把複雜轉為簡單，以清楚的方式撰寫程式碼（~~取代很潮、炫技~~），聽到這個段落，覺得特別有感。

[第二週作品](https://www.billyji.com/react-workshop-week2/)

大致的流程為：
1. 思考資料與切版（練習 Tailwind）
2. 程式邏輯撰寫 
3. 拆元件

----

## 思考資料與切版（練習 Tailwind）

在開始動工之前，先來初步釐清會有什麼資料（功能）：

- 菜單資料
  - `const [menu] = useState(data)`，放進 data 初始資料
  - 根據菜單的點擊，會帶 id、品項、描述、單價等等資料到購物車
  - 同一品項重覆點選時（該品項已經存在購物車），則在購物車資料中，將該品項數量 + 1

- 購物車資料
  - `const [cart, setCart] = useState([])`，初始購物車為空 Array
  - 除了菜單帶過來的資料外，也要多定義數量 `qty` 跟小計（數量*價錢）
  - 刪除品項跟更新品項數量的功能
  - 定義購物價錢總計 `const [sum, setSum] = useState(0)`
  - 備註 `const [note, setNote] = useState("")`; 
  - 點擊送出訂單時，會帶購物車資料到訂單

- 訂單資料： 
  - `const [order, setOrder] = useState([])`，初始為空 Array
  - 除了從購物車帶過來的資料，也多定義一個 id

切版：
因為先前的工作內容，所以較常在手刻畫面；不過近期回來接觸前端後，有時候反而會思考，有沒有什麼方式可以減少切版的時間（~~可能是開始想懶惰了?~~）。記得幾個月前還有看到一個付費的功能，是把 figma 的設計圖，轉換成 react 的程式碼。
藉由這樣的實作機會跟想法，決定嘗試使用 Tailwind，記得 JIT 模式剛出時有稍微接觸過。本來預期習慣 Tailwind 不會花太多時間，不過實際上還是花了不少時間。~~（也有去找人家寫好的 UI 來改）~~

## 程式邏輯撰寫

因為切版後的程式行數沒有很多(2xx)，所以就沒有先拆元件。

### 將 menu 定義到畫面上

```jsx
const App = () => {
  const [menu] = useState(data); // 原始 menu
  
  return (
    // ...略
    <ul className="flex flex-wrap -mx-3">
      {menu.map((item) => {
        return (
          <li key={item.id} className="md:w-1/2 w-full px-3 pb-6">
            <div className="rounded-lg border-2 p-3 cursor-pointer border-brown-light hover:bg-brown-100">
              <h3 className="text-xl text-brown font-medium">
                {item.name} <span>${item.price}</span>
              </h3>
              <p className="mt-1 text-md text-brown">
                {item.description}
              </p>
            </div>
          </li>
        );
      })}
    </ul>
    // ...略
  )

}

```

### 選擇飲品點擊時，將資料帶入購物車

- 購物車資料預設為 []
- 在選擇飲品的 List，新增 onClick 事件觸發 addCart，並在參數中帶上資料 (menu item)
- 在 addCart function 做一個 tempCart 的新陣列，並多帶上預設數量 qty 

```jsx
const App = () => {
  const [menu] = useState(data); // 原始 menu
  const [cart, setCart] = useState([]); // 購物車資料

  const addCart = (item) => {
    const tempCart = [
      ...cart, // [] 淺拷貝
      {
        ...item,
        qty: 1, // 數量預設為 1
      },
    ];
    setCart(tempCart);
  };

  return (
    // ...略
    <ul className="flex flex-wrap -mx-3">
      {menu.map((item) => {
        return (
          <li
            key={item.id}
            className="md:w-1/2 w-full px-3 pb-6"
            onClick={() => addCart(item)}
          >
            <div className="rounded-lg border-2 p-3 cursor-pointer border-brown-light hover:bg-brown-100">
              <h3 className="text-xl text-brown font-medium">
                {item.name} <span>${item.price}</span>
              </h3>
              <p className="mt-1 text-md text-brown">
                {item.description}
              </p>
            </div>
          </li>
        );
      })}
    </ul>
    // ... 略
  )

}
```

### 購物車資料定義到畫面

當我們可以透過點擊飲品，把飲品項目加到購物車，接著把購物車資料定義到畫面中。
（可以透過 [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=zh-TW) 工具來看資料）

```jsx
const App = () => {
  const [menu] = useState(data); // 原始 menu
  const [cart, setCart] = useState([]); // 購物車資料

  const addCart = (item) => {
    const tempCart = [
      ...cart, // [] 淺拷貝
      {
        ...item,
        qty: 1, // 數量預設為 1
      },
    ];
    setCart(tempCart);
  };

	return (
    // ...略
    <tbody>
      {cart.map((item) => {
        return (
          <tr key={item.id}>
            <td className="p-3 border-b border-dashed border-brown-light">
              <h3 className="text-xl text-brown font-medium">
                {item.name}
              </h3>
              <p className="mt-1 text-md text-brown">
                {item.description}
              </p>
              <button
                type="button"
                className="mt-2 text-red-400 text-sm"
              >
                刪除品項
              </button>
            </td>
            <td className="p-3 border-b border-dashed border-brown-light text-center text-md text-brown w-32">
              <select
                name=""
                id=""
                className="w-1/2 h-[32px] rounded-md text-center bg-brown-100 focus:outline-none focus:border-brown-dark"
                value={item.qty}
              >
                {[...Array(10).keys()].map((item) => {
                  return (
                    <option value={item + 1} key={item}>
                      {item + 1}
                    </option>
                  );
                })}
              </select>
            </td>
            <td className="p-3 border-b border-dashed border-brown-light text-center text-md text-brown">
              {item.price}
            </td>
            <td className="p-3 border-b border-dashed border-brown-light text-center text-md text-brown">
              {item.price * item.qty}
            </td>
          </tr>
        );
      })}
    </tbody>
  )

}
```

### 判斷購物車是否為空

以 `cart.length === 0` 來判斷要顯示的畫面

```jsx
{ cart.length === 0 
  ? (
  <div className="rounded-lg border-2 border-brown-light mt-4 p-4 text-center text-brown font-bold">
    尚未選擇飲品～
  </div>
  ) : (
    // ... 略
    {cart.map((item) => {
      return (
        <tr key={item.id}>
          <td className="p-3 border-b border-dashed border-brown-light">
            <h3 className="text-xl text-brown font-medium">
              {item.name}
            </h3>
            <p className="mt-1 text-md text-brown">
              {item.description}
            </p>
            <button
              type="button"
              className="mt-2 text-red-400 text-sm"
            >
              刪除品項
            </button>
          </td>
          // ... 略
        </tr>
      );
    })}
  )
}
```

### 重覆點選飲品的判斷

當我們點擊飲品，觸發飲品項目加到購物車時，還需判斷購物車中是否已經有此飲品品項，所以會以 id 作為判斷依據。

程式上先以 `const conformIndex = cart.findIndex((cartItem) => item.id === cartItem.id);` 來做判斷，對比飲品品項的 id 跟購物車 id。 (-1 表示購物車還沒有這個品項)

購物車沒有此品項時，就跑原本的新增程式；如果已經有此品項，就跑編輯的程式，編輯的寫法跟上週是一樣的，這邊就不再贅述。

```jsx
const addCart = (item) => {
  const conformIndex = cart.findIndex((cartItem) => item.id === cartItem.id);

  if (conformIndex === -1) {
    // -1 表示購物車還沒有這個品項
    const tempCart = [
      ...cart, // [] 淺拷貝
      {
        ...item,
        qty: 1, // 數量預設為 1
      },
    ];
    setCart(tempCart);
  } else {
    // 購物車原先就有此品項
    const tempCart = cart.map((cartItem) => {
      return item.id === cartItem.id
        ? {
            ...cartItem,
            qty: cartItem.qty < 10 ? cartItem.qty + 1 : cartItem.qty,
          }
        : { ...cartItem };
    });
    setCart(tempCart);
  }
};
```

### 購物車總計

先定義 `const [total, setTotal] = useState(0);` 總計資料，然後透過 useEffect 來觀察購物車是否有更動，只要更動就透過 reduce 重新計算總價。
（這邊就省略把 total 放到畫面上）

```jsx
const App = () => {
	const [menu] = useState(data); // 原始 menu
	const [cart, setCart] = useState([]); // 購物車資料
	const [total, setTotal] = useState(0); // 總計

  // ... 略
  useEffect(() => {
		const totalPrice = cart.reduce((prev, curr) => {
			return prev + curr.price * curr.qty;
		}, 0);
		setTotal(totalPrice);
	}, [cart]);

  return (
    //...略
  )

```

### 購物車品項更改數量 (select)

如果我們在 select 綁上 value ，但並沒有綁上 onChange 的功能時，雖然程式並不會整個爆掉，但會噴紅字加上不能操作 select。

所以在這邊加上 updateCart 的功能，記得帶上 `e.target.value`。

```jsx
const App = () => {
  const [menu] = useState(data); // 原始 menu
  const [cart, setCart] = useState([]); // 購物車資料
  const [total, setTotal] = useState(0); // 總計

  // ... 略

  const updateCart = (item, value) => {
    const tempCart = cart.map((cartItem) => {
      return item.id === cartItem.id
        ? {
            ...cartItem,
            qty: Number(value),
          }
        : { ...cartItem };
    });
    setCart(tempCart);
  };

  return (
    // ... 略
    <td className="p-3 border-b border-dashed border-brown-light text-center text-md text-brown w-32">
      <select
        name=""
        id=""
        className="w-1/2 h-[32px] rounded-md text-center bg-brown-100 focus:outline-none focus:border-brown-dark"
        value={item.qty}
        onChange={(e) => updateCart(item, e.target.value)}
      >
        {[...Array(10).keys()].map((item) => {
          return (
            <option value={item + 1} key={item}>
              {item + 1}
            </option>
          );
        })}
      </select>
    </td>
    // ...略
  )
}

```

### 刪除購物車品項

那時上課時，看到用 filter 來處理刪除功能，還是會有點反應不上來，雖然並不是第一次看到這樣操作。

筆記：`item.id === cartItem.id` 表示符合條件的只有一筆；所以反過來寫的話，就是除了這一筆之外的資料都要留著。
（畫面略)
```jsx
const deleteCartItem = (item) => {
  const tempCart = cart.filter((cartItem) => {
    return item.id !== cartItem.id;
  });
  setCart(tempCart);
};
```

### 創建訂單

當我們點擊購物列表的送出按鈕後，會去觸發 createOder function。

- 定義資料 `const [order, setOrder] = useState([]);`
- 在 tempOrder 這個 array 中，除了帶上購物車、備註、總計的資料外，也多定義一個 id
- 最後記得清空購物車跟備註資料

```jsx
const App = () => {
  const [menu] = useState(data); // 原始 menu
  const [cart, setCart] = useState([]); // 購物車資料
  const [total, setTotal] = useState(0); // 總計
  const [note, setNote] = useState(""); // 備註
  const [order, setOrder] = useState([]); // 訂單

  // ... 略

  const createOder = () => {
    const tempOrder = [
      ...order, 
      {
        id: new Date().getTime(),
        cart,
        note,
        total,
      },
    ];

    setOrder(tempOrder);
    setCart([]);
    setNote("");
  };
}
```

### 定義訂單到畫面

先用 `order.length === 0` 判斷要顯示什麼畫面。接著把資料帶到畫面中。

```jsx
  <div className="mx-auto lg:w-3/5 w-full">
    {/* 訂單  */}
    {order.length === 0 ? (
      <div className="rounded-lg border-2 border-brown-light mt-4 p-4 text-center text-brown font-bold">
        尚未建立訂單～
      </div>
    ) : (
      order.map((item) => {
        return (
          <div
            className="rounded-lg border-2 border-brown-light mt-4 p-4 text-brown text-center"
            key={item.id}
          >
            <h2 className="text-2xl font-bold text-primary text-left">
              訂單
            </h2>
            <p className="mt-1 text-md text-brown text-left">
              編號：{item.id}
            </p>
            <p className="mt-1 text-md text-brown text-left">
              備註：{item.note}
            </p>
            <div className="mt-2 mb-4">
              <table className="w-full">
                <thead className="border-b-2 border-brown-light">
                  <tr>
                    <th className="p-3 font-medium text-brown">品項</th>
                    <th className="p-3 font-medium text-brown">數量</th>
                    <th className="p-3 font-medium text-brown">小計</th>
                  </tr>
                </thead>
                <tbody className="">
                  {item.cart.map((innerItem) => {
                    return (
                      <tr
                        key={innerItem.id}
                        className="border-b border-brown-light"
                      >
                        <td className="p-3 text-brown">
                          {innerItem.name}
                        </td>
                        <td className="p-3 text-brown">
                          {innerItem.qty}
                        </td>
                        <td className="p-3 text-brown">
                          {innerItem.qty * innerItem.price}
                        </td>
                      </tr>
                    );
                  })}
                </tbody>
                <tfoot>
                  <tr>
                    <td colSpan="2"></td>
                    <td colSpan="1" className="p-3">
                      <h3 className="text-center text-xl text-brown font-medium">
                        總計: ${item.total}
                      </h3>
                    </td>
                  </tr>
                </tfoot>
              </table>
            </div>
          </div>
        );
      })
    )}
  </div>
```

## 拆元件

處理好程式邏輯後，再來就是拆元件的部分，這邊就先拆出最基準的三個（Menu、Cart、Order）。

在拆元件之前，因為 ESLint 會提醒 props 的型別宣告，所以要先安裝 [prop-types](https://www.npmjs.com/package/prop-types)。

### 以 Cart 為範例

這邊就以購物車的元件作為主要範例，其他元件拆法都是一致的。首先我們先在 src 新增 components 資料夾，並新增元件(Cart.jsx)。

接著在 Cart.jsx 先建好預設，並 import propTypes 以及預設 propTypes 物件：
```jsx 
// Cart.jsx
import propTypes from "prop-types";

const Cart = () => {
  return ()
}

Cart.propTypes = {
	
};

export default Cart;
```

將 App.jsx 中的購物車內容，移動到 Cart.jsx 的樣板中。
然後在 App.jsx 引入 Cart.jsx


```jsx 
// Cart.jsx
import propTypes from "prop-types";
const Cart = () => {
return (
  <div className="">
    {/* 購物車 */}
    <h2 className="text-2xl font-bold text-primary">購物列表</h2>
    <hr className="mt-2 mb-4" />
    {cart.length === 0 ? (
      <div className="rounded-lg border-2 border-brown-light mt-4 p-4 text-center text-brown font-bold">
        尚未選擇飲品～
      </div>
    ) : (
      <div className="rounded-lg border-2 border-brown-light mt-4 p-4">
        <table className="min-w-full leading-normal">
          <thead>
            <tr>
              <th className="p-3 text-left text-xs font-semibold text-brown-light">
                飲品資訊
              </th>
              <th className="p-3 text-center text-xs font-semibold text-brown-light">
                數量
              </th>
              <th className="p-3 text-center text-xs font-semibold text-brown-light">
                單價
              </th>
    // ... 以下略
);
};

```

```jsx 
// App.jsx
import { useState, useEffect } from "react";
import Cart from "@/components/Cart";
// ... 略

const App = () => {
  // ... 略
  return (
    <div className="bg-[#F8F6F2] min-h-screen">
      <div className="container mx-auto px-3 py-8">
        <div className="grid grid-cols-1 gap-4 lg:grid-cols-2 lg:gap-8">
          // 略 ...
          <Cart />
        </div>
        <hr className="mt-4 mb-6" />
        // ...略
      </div>
    </div>
  );
}

```

在 Cart.jsx 定義好預計要傳過來的資料跟功能，如下圖所示，我們可以根據紅字（沒有定義）來知道預計要傳過來的資料跟功能。接著透過 Cart.propTypes 定義好要傳進來的資料型別、功能

![](https://i.imgur.com/WCPjccp.png)

```jsx 
// Cart.jsx
import propTypes from "prop-types";
const Cart = ({ cart, deleteCartItem, updateCart, total, note, setNote,
createOder }) => {
  return (
    <div className="">
      {/* 購物車 */}
      <h2 className="text-2xl font-bold text-primary">購物列表</h2>
      <hr className="mt-2 mb-4" />
    // ... 略
  )
}

Cart.propTypes = {
  cart: propTypes.array.isRequired,
  deleteCartItem: propTypes.func.isRequired,
  updateCart: propTypes.func.isRequired,
  total: propTypes.number.isRequired,
  note: propTypes.string.isRequired,
  setNote: propTypes.func.isRequired,
  createOder: propTypes.func.isRequired,
};
```

根據在 Cart.jsx 所需要的資料或功能，我們就可以從父層傳入相對應的資料跟功能。

```jsx 
// app.jsx
import Cart from "@/components/Cart";
// ...略

const App = () => {
  // ... 略
  return (
    <div className="bg-[#F8F6F2] min-h-screen">
      <div className="container mx-auto px-3 py-8">
        <div className="grid grid-cols-1 gap-4 lg:grid-cols-2 lg:gap-8">
          // ... 略
          <Cart
            cart={cart}
            deleteCartItem={deleteCartItem}
            updateCart={updateCart}
            total={total}
            note={note}
            setNote={setNote}
            createOder={createOder}
          />
        </div>
        <hr className="mt-4 mb-6" />
        // ... 略
      </div>
    </div>
  );
}

```


----

## 結尾

現在在寫 React 時，都特別能感受自己原生 JS 的不足，雖然能寫出程式，不過有時都會覺得卡卡的。先前寫 Vue 並不會這樣覺得，可能是因為 Vue 幫我們做了很多事（黑魔法）。

在寫這週紀錄的結尾時，就在想第三週跟第四週可能會換種方式紀錄，雖然這樣的步驟拆解，可以讓我刻意練習思路；主要是考量到自己的其他規劃，不過目前倒還不曉得要改用什麼方式，也許下週就會有靈感了（？）。

（若有什麼部分寫錯，也再麻煩跟我說，感恩！）



