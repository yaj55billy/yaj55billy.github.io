---
title: 六角學院 React 入門工作坊第三週
date: 2023-08-28
excerpt: 關於第三週的一些分享、心得
index_img: https://i.imgur.com/Zwb2dxR.jpg
banner_img: https://i.imgur.com/Zwb2dxR.jpg
tags: [六角學院, React]
categories: [前端]
---

Photo by <a href="https://unsplash.com/@lautaroandreani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Lautaro Andreani</a> on <a href="https://unsplash.com/photos/xkBaqlcqeb4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

## 前言

第三週因為事情比較多，所以整體的進度慢了一些。上課也只能跟到前半段 30 分鐘，覺得曠掉直播課是滿可惜的(QQ)，而且也可能會錯過~~尿點~~知識；後來在隔天老師上傳錄影檔時，就趕快補上進度，這週的主軸核心為 API 串接，可以好好做個練習，上課時老師也會補上一些知識點（ex: JWT），並講解的比較好理解。

這週因為一些規劃跟時間關係，所以會跟前兩週完整拆解的方式不同，這邊抽出三個部分來分享：

- React Hook Form
- Axios 的 API 管理
- 關於 API 串接練習

[第三週作品](https://www.billyji.com/react-workshop-week3/)

---

## React Hook Form

一開始在寫註冊這個元件時，忽然興起了找表單套件的想法，後來就找到了[React Hook Form](https://react-hook-form.com/)，也決定在這次作業中來嘗試一下。

### 起手安裝

```
npm install react-hook-form
```

### 引用

1. 在元件中透過 `import { useForm } from "react-hook-form"`，從表單套件引用 useForm 這個功能
2. 接著從 useForm 的執行中，解構出 register 跟 handleSubmit。（前者處理資料，後者處理表單提交相關）
3. 再來定義好表單送出的 onSubmit 函式，這是屬於自定義的函式，所以命名也是自定義的。稍後就會提到這個自定義函式的 data 參數。

```jsx
import { useForm } from "react-hook-form";

const SignUp = () => {
	const {
		register, // state
		handleSubmit, // submit
	} = useForm();

	const onSubmit = (data) => {
		// 自定義的 submit
		console.log(data);
	};

	return <>...略</>;
};
```

### 在 jsx 中使用

接著我們來看 jsx 樣板部分：

1. 如稍早所述 register 是處理資料相關，它用來建立表單元素跟資料的關聯。在下方程式碼的三個 input 中，我們都能看到 `{...register("欄位命名")}` 這樣的方式，將 state 登記（綁定）到某個表單元素。
2. 而在 form tag 的部分，`handleSubmit` 是處理表單提交的函式，透過這樣 `{handleSubmit(onSubmit)}` 把我們自定義的函式丟進去，就能在表單送出後，在自定義函式的參數中，得到 `register` 的資料。

（爲了方便理解，所以有稍微省略一些程式碼結構）

```jsx
return (
	<div id="signUpPage" className="bg-yellow">
		<form className="formControls" action="" onSubmit={handleSubmit(onSubmit)}>
			<h2 className="formControls_txt">註冊帳號</h2>
			<input
				className="formControls_input"
				type="email"
				id="email"
				placeholder="請輸入 email"
				{...register("email")}
			/>

			<input
				className="formControls_input"
				type="text"
				id="nickname"
				placeholder="請輸入您的暱稱"
				{...register("nickname")}
			/>

			<input
				className="formControls_input"
				type="password"
				id="password"
				placeholder="請輸入密碼"
				{...register("password")}
			/>

			<button className="formControls_btnSubmit" type="submit">
				註冊帳號
			</button>
		</form>
	</div>
);
```

補充：
從以上範例中，當我們填寫好 input 並送出表單後，就能從自定義函式的 `data` 參數中取得資料。

```js
// 自定義的 submit
const onSubmit = (data) => {
	console.log(data);
	// {email: '我們所填入的 mail', nickname: '填入的 nickname', password: '填入的密碼'}
};
```

### 使用心得

這週上課時，中途有提到表單處理，雖然 React 在資料處理上有一些固定的模式，但不得不說是比較複雜的（很吃 JS 底子）。

這次作業的表單是較為單純的（欄位少），所以即使不用這個套件，對於速度跟方便性上也不會差異太多；不過透過這樣的嘗試，可以知道如果是複雜表單（欄位多），就相當適合導入使用，而這個套件也包含了一些方便的功能，像是狀態監聽、錯誤樣式、驗證等等。

## Axios 的 API 管理

當我們使用 axios 來串接 api 時，可以試著把 api crud 的相關操作都集中在一支 js 檔案管理，需要某個 api 操作時再引用出來，這樣的概念跟優點可以參考 [Mike 老師的文章](https://medium.com/i-am-mike/%E4%BD%BF%E7%94%A8axios%E6%99%82%E4%BD%A0%E7%9A%84api%E9%83%BD%E6%80%8E%E9%BA%BC%E7%AE%A1%E7%90%86-557d88365619)。

自己最早接觸到這樣的概念是幾年前的某次面試，那時跟負責面試的技術主管討論了工作上的專案，以及聊到了在 Vue 直播班做的作品（2020 的直播班），而這位主管在面試快結束時，也給予我一些建議，像是可以鑽研 vue 的底層知識、vue2 到 vue3 的不同，以及 api 抽出來管理（真的很感謝那位技術主管）。雖然這次課程用到的 api 數量不多，所以即使沒有管理也是 OK 的，不過還是借著這樣的機會來做練習。

### 抽出 api 程式

1. 看了 api 操作有哪些之後，從專案根目錄開一個 api folder，並在裡頭新增 index.js
2. 首先 import axios 到這支檔案中，並透過 axios.create 創建 axios 的實體
   - 雖然這次只用到 baseURL，不過還是可以看一下文件的 [config](https://github.com/axios/axios#request-config)
3. 把 api 的 crud 操作包裝在函式內，並 export 出去。這邊也先根據 api 文件去配置需要的參數，像是 data、id、headers
   - 在函式命名上我以 api 做開頭，表示這是處理 api 相關

```js
// api/index.js
import axios from "axios";

const todoBase = axios.create({
	baseURL: "https://...",
});

// 註冊
export const apiUsersSignUp = (data) => todoBase.post("/users/sign_up", data);

// 登入
export const apiUsersSignIn = (data) => todoBase.post("/users/sign_in", data);

// 驗證
export const apiUsersCheckout = (headers) =>
	todoBase.get("/users/checkout", headers);

// 登出
export const apiUsersSignOut = (headers) =>
	todoBase.post("/users/sign_out", {}, headers);

// 取得所有待辦
export const apiGetTodos = (headers) => todoBase.get("/todos/", headers);

// 新增待辦
export const apiPostTodos = (data, headers) =>
	todoBase.post("/todos/", data, headers);

// 刪除待辦
export const apiDeleteTodos = (id, headers) =>
	todoBase.delete(`/todos/${id}`, headers);

// 切換待辦狀態
export const apiPatchTodos = (id, headers) =>
	todoBase.patch(`/todos/${id}/toggle`, {}, headers);

// 編輯待辦文字
export const apiPutTodos = (id, data, headers) =>
	todoBase.put(`/todos/${id}`, data, headers);
```

### 引用

再來以新增項目跟刪除項目來做個範例：

1. 首先從 api/index.js 引用我們需要的 api 函式，這個範例是新增跟刪除項目的函式
2. 新增項目：在 addTodo 中去使用 apiPostTodos 這個操作，並帶上需要的 data(input value) 跟 headers(token)。後面一樣可接上 .then().catch()
3. 刪除項目：在 deleteTodo 中去使用 apiDeleteTodos 這個操作，並帶上需要的 id(要刪除哪一筆) 跟 headers

（範例中為了說明主要內容，所以會省略較多程式碼）

```jsx
// Todo.jsx
// ...略
import { apiPostTodos, apiDeleteTodos } from "../api";

const Todo = ({ todoToken }) => {
	// ...略
	const [input, setInput] = useState("");

	const headers = {
		Authorization: todoToken,
	};

	// ...略

	// 新增項目
	const addTodo = () => {
		if (input === "") return;
		apiPostTodos({ content: input }, { headers }).then((res) => {
			console.log(res);
		});

		setInput(""); // 清空
		getTodos();
	};

	// 刪除項目
	const deleteTodo = (id) => {
		apiDeleteTodos(id, { headers });
		getTodos();
	};
	// ... 略
};

export default Todo;
```

### 題外話跟補充

雖然知道 Async/Await 這個寫法在視覺上比較好讀，但不曉得為何自己一直都很習慣用 .then().catch() 的形式。
補充：如果不是用 axios 來串接 api，也是可以做到管理的，不過要自己多寫些程式封裝。

## 關於 API 串接練習

在寫這篇文的過程中，也看到老師在公告中，建議同學盡量練習課程所提供的 API。我覺得這樣的練習相當重要，除了 AJAX 是前端的必須技能外，我也想分享這幾年在前端工作的心得。

前端工程師的職務內容，在不同公司跟產業中是差異很大的，再加上團隊氛圍等多種因素的影響。有的公司很重視切版、畫面，有的則很注重程式，有些時候也會被指派一些跟前端無關的任務。以我自己上一份工作經驗來說，大約有 10% 到 20% 的時間會做非程式任務，20% 的時間會處理一些後端部分（例如將表單資料送入資料庫），而剩下的部分才回到前端領域。（補充：這個段落不是抱怨，而是接受每個公司對前端需求是不同的，主要是當事者能否接受）

由於公司的類型多樣，比如團隊是否有前後端分離，或者專案的時辰充裕與否，再加總一些其他因素等等，我們不一定能在工作上串接到後端提供的 API，再來是不一定會有這麼詳盡的文件（~~甚至沒有文件~~）。所以如第一個段落所述，會覺得這樣的練習是很重要。
（當然也有一些第三方服務是可以串接練習的）

---

## 結尾

第三週作業如果寫的較為完整，就可以好好來銜接最終作業了。不曉得為何，這週寫作業時，腦袋會比較頻繁的當機，所以都要提醒自己起來動一動，做點不同的事情。

最近回來寫程式的過程（~~復健~~），一直能感覺到過去累積不少技術債，這陣子先專心在規劃好的任務上，之後再來還債了（例如：JS 核心）。

（若有什麼部分寫錯，也再麻煩跟我說，感恩！）
