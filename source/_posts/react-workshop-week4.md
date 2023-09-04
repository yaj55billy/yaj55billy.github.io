---
title: 六角學院 React 入門工作坊第四週（最終）
date: 2023-08-31
tags:
  - 前端
  - 六角學院 
  - React
categories:
  - 前端
description: 第四週與拆解
cover: https://i.imgur.com/Zwb2dxR.jpg
---

Photo by <a href="https://unsplash.com/@lautaroandreani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Lautaro Andreani</a> on <a href="https://unsplash.com/photos/xkBaqlcqeb4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

## 前言

這週很有感的課程內容：
React 的優點是體驗 JS 的美好，而缺點也是 JS 的美好；Vue 的優點是幫開發者做了很多，而缺點也是幫開發者做了很多。
這段很符合我學習 Vue 跟 React 的心情寫照，2020 參加六角的 Vue 直播班時，剛開始覺得 Vue 的語法有點麻煩，要記滿多規矩的，雖然後來習慣就好了；而今年接觸 React 時，剛開始覺得好自由（~~真香~~），但隨著接觸時間稍長，就開始想起 Vue 的好 XD （框架都是工具，最重要的還是要達成什麼） 

因為已經有了第三週的打底，所以這週的作業時間就會比較短。這邊先拆解一下流程：
- React Router：初步分好註冊、登入、Todo 頁面
- 刻畫面：這個部分就不列在文章內容中。本來第三週還滿懶惰的，所以是拿課程範例來改；不過等到要做最終作業時，又有點手癢想手刻一下...
- 註冊頁面
  - 除了原先的註冊 api，也會引入 SweetAlert2 做註冊成功或失敗的 popup 提示 (全部頁面都會使用)
  - 註冊成功後，導向到登入頁面
  - 防止註冊按鈕被連續點擊
- 登入頁面
  - 登入成功與失敗，會用 popup 做提示。登入成功時，設置秒數導至 todo 頁面
  - 登入成功後取得 token，並儲存在 document.cookie
  - 防止登入按鈕被連續點擊
- Todo 頁面
  - state 定義
  - 驗證 api：驗證成功就可以使用 todo，如果驗證失敗，就用 popup 提醒使用者，並導回登入頁
  - 取得待辦資料 (getTodo)
  - 待辦的新增、刪除、編輯、狀態切換（成功與失敗的提醒）
  - 全部、待完成、已完成的待辦篩選
  - 從 todo 分出的元件 
  - 待完成待辦的數量顯示、清除已完成項目功能
    - （2023.09.04 調整，原先寫錯成"已完成待辦的數量顯示"，程式以及這篇文章已調整為"待完成"）
  - 登出，popup 提醒使用者，然後導到登入頁

[第四週作品](https://www.billyji.com/react-workshop-week4-todolist/#/)
[第四週程式碼](https://github.com/yaj55billy/react-workshop-week4-todolist)

----

## React Router

首先在專案中，引入 React Router `npm i react-router-dom`，然後在 main.jsx 進入點，從 React Router 引用 `<HashRouter>` 並包在 `<App>` 的外層。

接著可以照著課程指導的步驟：
1. 建立元件（頁面元件放在 view 資料夾）
2. 設置路由表 （import 元件）
3. 加入連結 （這邊不會用到）

```jsx main.jsx
import ReactDOM from "react-dom/client";
import { HashRouter } from "react-router-dom";
import App from "./App.jsx";

ReactDOM.createRoot(document.getElementById("root")).render(
  <HashRouter>
    <App />
  </HashRouter>
);
```

```jsx App.jsx
import { Routes, Route } from "react-router-dom";
import "./styles/all.sass";
import SignUp from "./views/SignUp.jsx";
import SignIn from "./views/SignIn.jsx";
import Todo from "./views/Todo.jsx";

function App() {
  return (
    <>
      <Routes>
        <Route path="/" element={<SignIn />} />
        <Route path="/signup" element={<SignUp />} />
        <Route path="/todo" element={<Todo />} />
      </Routes>
    </>
  );
}
export default App;

```

## API

跟第三週一樣把 api 獨立成一支 js 來管理，不過內容有一些不同，因為在 todo 頁面驗證後，會在 todoBase 這個 axios 實體帶入以下這個設定：`todoBase.defaults.headers.common["Authorization"] = todoToken`，所以在 todo 頁面用到的 api 就可以省略 headers 這個參數

```js api/index.js
import axios from "axios";

// todolist api
export const todoBase = axios.create({
  baseURL: "https://todolist-api.hexschool.io",
});

// 註冊
export const apiUsersSignUp = (data) => todoBase.post("/users/sign_up", data);

// 登入
export const apiUsersSignIn = (data) => todoBase.post("/users/sign_in", data);

// 驗證
export const apiUsersCheckout = (headers) => todoBase.get("/users/checkout", headers);

// 登出
export const apiUsersSignOut = () => todoBase.post("/users/sign_out", {});

// 取得所有待辦
export const apiGetTodos = () => todoBase.get("/todos/");

// 新增待辦
export const apiPostTodos = (data) => todoBase.post("/todos/", data);

// 刪除待辦
export const apiDeleteTodos = (id) => todoBase.delete(`/todos/${id}`);

// 切換待辦狀態
export const apiPatchTodos = (id) => todoBase.patch(`/todos/${id}/toggle`, {});

// 編輯待辦文字
export const apiPutTodos = (id, data) => todoBase.put(`/todos/${id}`, data);

```

## 註冊

這邊單純呈現註冊頁的畫面部分：

```jsx signUp.jsx
// ... 略

return (
  <div id="sign-up" className="content">
    <div className="content__header">
      <h2 className="content__title">註冊帳號</h2>
    </div>
    <div className="content__body">
      <form className="form" onSubmit={handleSubmit(onSubmit)}>
        <div className="form__item">
          <label className="form__label" htmlFor="signupEmail">
            Email
          </label>
          <input
            className="form__input"
            type="email"
            id="signupEmail"
            placeholder="請輸入 email"
            required
            {...register("email")}
          />
        </div>
        <div className="form__item">
          <label className="form__label" htmlFor="nickname">
            您的暱稱
          </label>
          <input
            className="form__input"
            type="text"
            id="nickname"
            placeholder="請輸入您的暱稱"
            required
            {...register("nickname")}
          />
        </div>

        <div className="form__item">
          <label className="form__label" htmlFor="signupPassword">
            密碼
          </label>
          <input
            className="form__input"
            type="password"
            id="signupPassword"
            placeholder="請輸入密碼"
            required
            {...register("password")}
          />
        </div>
        <div className="text-center mt-6">
          <button className="form__button" type="submit">
            註冊帳號
          </button>
        </div>
        <div className="text-center mt-3">
          已有帳號了？<NavLink to="/">登入</NavLink>
        </div>
      </form>
    </div>
  </div>
);

```

再來是註冊的程式部分：
- 當填好表單按下註冊按鈕時，會執行 onSubmit 這個函式，並在參數中帶進表單資料，接著就會運行註冊的 api 
- 註冊成功或失敗時，會使用 sweetalert2 引入的 popup 來通知使用者
- 註冊成功或失敗時，會將表單欄位清空。這邊是從 useForm 引出 reset 的功能
- 註冊成功時，會透過 navigate 導到登入頁面
- `const [isDisabled, setDisabled] = useState(false);` 這個狀態的定義，主要是綁在註冊按鈕的 disabled，在 api 過程讓按鈕為不能點擊的狀態，直到 api 過程結束（防止按鈕被連續點擊）

```jsx signUp.jsx
import Swal from "sweetalert2";
import { useState } from "react";
import { NavLink, useNavigate } from "react-router-dom";
import { useForm } from "react-hook-form";
import { apiUsersSignUp } from "../api";

const SignUp = () => {
  const [isDisabled, setDisabled] = useState(false);
  const { register, handleSubmit, reset } = useForm();

  const resetHandle = () => {
    reset();
  };

  const navigate = useNavigate();

  const onSubmit = (data) => {
    setDisabled(true);
    apiUsersSignUp(data)
      .then(() => {
        Swal.fire({
          title: "恭喜註冊成功！",
          text: "為您導至登入頁...",
          icon: "success",
          showConfirmButton: false,
          timer: 1500,
        });
        setTimeout(() => {
          navigate("/");
        }, 1500);
      })
      .catch((error) => {
        Swal.fire({
          title: error.response.data.message,
          icon: "error",
          showConfirmButton: false,
          timer: 1500,
        });
      })
      .finally(() => {
        setDisabled(false);
        resetHandle();
      });
  };
  return(
    // ... 略
  )
}
```


## 登入

跟註冊頁面相似，所以這邊只呈現程式部分：
- 登入成功或失敗時，會使用 sweetalert2 引入的 popup 來通知使用者
- 登入成功或失敗時，會將表單欄位清空。這邊是從 useForm 引出 reset 的功能
- 登入成功後，會從回應資料取出 token，並存到 document.cookie，待之後 todo 頁面再取出
- 登入成功後，使用 navigate 導向到 todo 頁面
- `const [isDisabled, setDisabled] = useState(false);` 這個狀態的定義，主要是綁在登入按鈕的 disabled，在 api 過程讓按鈕為不能點擊的狀態，直到 api 過程結束（防止按鈕被連續點擊）

```jsx signIn.jsx
import Swal from "sweetalert2";
import { useState } from "react";
import { NavLink, useNavigate } from "react-router-dom";
import { useForm } from "react-hook-form";
import { apiUsersSignIn } from "../api";

const SignIn = () => {
  const [isDisabled, setDisabled] = useState(false);
  const { register, handleSubmit, reset } = useForm();

  const resetHandle = () => {
    reset();
  };

  const navigate = useNavigate();

  const onSubmit = (data) => {
    setDisabled(true);
    apiUsersSignIn(data)
      .then((res) => {
        const { token } = res.data;
        const tomorrow = new Date();
        tomorrow.setDate(tomorrow.getDate() + 1);
        document.cookie = `token=${token};expires=${tomorrow.toUTCString()}`;
        Swal.fire({
          title: "登入成功！",
          text: "為您導至 Todo 頁面...",
          icon: "success",
          showConfirmButton: false,
          timer: 1500,
        });
        setTimeout(() => {
          navigate("/todo");
        }, 1500);
      })
      .catch(() => {
        Swal.fire({
          title: "登入失敗",
          text: "請檢查帳密是否正確，或是否有註冊過。",
          icon: "error",
          showConfirmButton: false,
          timer: 1500,
        });
      })
      .finally(() => {
        setDisabled(false);
        resetHandle();
      });
  };
}
```

## Todo 頁

### state 定義

首先我們先定義好會用到的 state：

- todo 資料：`const [todo, setTodo] = useState([])`  
- todo 資料的分類（全部、待完成、已完成）：`const [todoType, setTodoType] = useState("all");` 
- 待辦事項 input：`const [input, setInput] = useState("")` 
- 用戶的 nickname：`const [nickname, setNickname] = useState("")` 
- 編輯的時候會使用上暫存物件：`const [editTarget, setEditTarget] = useState({})` 

### 驗證 api

進到 todo 頁面時，首先需先經過 token 的驗證，驗證成功才能在此頁面操作，否則就會通知驗證失敗並導回登入頁。

1. 從 document.cookie 取出剛才在登入成功時設置的 token，並存取在 todoToken 這個變數中
2. 設置 checkLogin 這個函式，內容會使用 apiUsersCheckout 這支驗證 api，記得要帶上 `headers:{Authorization: todoToken}`
    - 驗證成功時會設置 nickname， 以及將 token 設置在 todoBase 這個實體，之後在 todo 頁面的 api 操作都不需帶上 `headers:{Authorization: todoToken}`，接著透過 getTodos() 獲取所有待辦項目。（這個步驟還未定義 getTodos()）
    - 驗證失敗時，會用 popup 來通知，之後導回登入頁面
3. 透過 useEffect 去執行 checkLogin 這個函式（第二個空陣列參數，表示只有最開始時會執行一次）

```jsx todo.jsx
import Swal from "sweetalert2";
import { useNavigate } from "react-router-dom";
import { useState, useEffect } from "react";
import {
  apiUsersCheckout,
  apiGetTodos,
  apiPostTodos,
  apiDeleteTodos,
  apiPatchTodos,
  apiPutTodos,
  todoBase,
  apiUsersSignOut,
} from "../api";

const Todo = () => {
  // ... 略
  const navigate = useNavigate();

  const todoToken = document.cookie
    .split("; ")
    .find((row) => row.startsWith("token="))
    ?.split("=")[1];

  const checkLogin = () => {
    apiUsersCheckout({
      headers: {
        Authorization: todoToken,
      },
    })
      .then((res) => {
        setNickname(res.data.nickname);
        todoBase.defaults.headers.common["Authorization"] = todoToken;
        getTodos();
      })
      .catch(() => {
        Swal.fire({
          title: "驗證失敗，請先登入",
          text: "稍後導至登入頁",
          icon: "error",
          showConfirmButton: false,
          timer: 1500,
        });
        setTimeout(() => {
          navigate("/");
        }, 1500);
      });
  };

  useEffect(() => {
    checkLogin();
  }, []);
  // ... 略
}

```


### 取得待辦資料 (getTodos)

再來我們要定義 getTodos 這個函式，內容透過 apiGetTodos() 這支取得所有待辦的 api 來取得資料。
不論在驗證後，以及新增、刪除、修改待辦等動作都會來執行這個函式，重新抓取最新資料。

```jsx todo.jsx
// ... 略
import { useState, useEffect } from "react";
import {
  apiUsersCheckout,
  apiGetTodos,
  apiPostTodos,
  apiDeleteTodos,
  apiPatchTodos,
  apiPutTodos,
  todoBase,
  apiUsersSignOut,
} from "../api";

const Todo = () => {
  const [todo, setTodo] = useState([]);
  // ... 略
  const getTodos = () => {
    apiGetTodos()
      .then((res) => {
        setTodo(res.data.data);
      })
      .catch((err) => {
        console.log(err);
      });
  };
  // ... 略
}

```

### 待辦的新增、刪除、編輯、狀態切換

在待辦的新增、刪除、編輯、狀態切換等函式中，除了從 api/index.js 取得各個 api 操作，也使用上 SweetAlert2 的 Toast 設置，在 api 執行後的成功或失敗做個通知。

提醒：
- 在每個 api 執行後，記得呼叫 getTodos()，重新獲得最新資料
- 待辦新增（addTodo）記得要清空 input（`setInput("")`）
- 刪除與編輯記得要帶上 id 
- 編輯待辦（updateTodo），記得要清空暫存的物件（`setEditTarget({})`）

```jsx todo.jsx
import Swal from "sweetalert2";
import { useNavigate } from "react-router-dom";
import { useState, useEffect } from "react";
import {
  apiUsersCheckout,
  apiGetTodos,
  apiPostTodos,
  apiDeleteTodos,
  apiPatchTodos,
  apiPutTodos,
  todoBase,
  apiUsersSignOut,
} from "../api";
const Todo = () => {
  const [todo, setTodo] = useState([]);
  const [todoType, setTodoType] = useState("all");
  const [input, setInput] = useState("");
  const [nickname, setNickname] = useState("");
  const [editTarget, setEditTarget] = useState({});

  // ...略
  const Toast = Swal.mixin({
    toast: true,
    position: "top-end",
    showConfirmButton: false,
    customClass: {
      popup: "colored-toast",
    },
    timer: 1500,
    timerProgressBar: true,
  });

  // 新增項目
  const addTodo = () => {
    if (input === "") return;
    apiPostTodos({ content: input })
      .then(() => {
        Toast.fire({
          icon: "success",
          title: "新增待辦成功",
        });
        getTodos();
      })
      .catch(() => {
        Toast.fire({
          icon: "error",
          title: "新增待辦失敗，請再檢查看看",
        });
      }).finally(() => {
        setInput(""); // 清空
        setTodoType("all"); // 將頁籤切換為「全部」
      });
  };

  // 刪除項目
  const deleteTodo = (id) => {
    apiDeleteTodos(id)
      .then(() => {
        Toast.fire({
          icon: "success",
          title: "刪除待辦成功",
        });
        getTodos();
      })
      .catch(() => {
        Toast.fire({
          icon: "error",
          title: "刪除待辦失敗，請再檢查看看",
        });
      });
  };

  // 切換狀態（是否完成）
  const toggleTodo = (id) => {
    apiPatchTodos(id)
      .then(() => {
        Toast.fire({
          icon: "success",
          title: "待辦狀態更新成功",
        });
        getTodos();
      })
      .catch(() => {
        Toast.fire({
          icon: "error",
          title: "待辦狀態更新失敗，請再檢查看看",
        });
      });
  };

  // 編輯項目
  const updateTodo = (id, content) => {
    apiPutTodos(id, { content })
      .then(() => {
        Toast.fire({
          icon: "success",
          title: "編輯待辦成功",
        });
        getTodos();
      })
      .catch(() => {
        Toast.fire({
          icon: "error",
          title: "編輯待辦失敗，請再檢查看看",
        });
      }).finally(() => {
        setEditTarget({});
      });
  };

  //...略
}
```

### 全部、待完成、已完成的待辦篩選

1. 如同我們前面定義的 `const [todoType, setTodoType] = useState("all");`，這個部分用來定義 todo 篩選的三種狀態：全部（all）、待完成（active）、已完成（completed）
2. 再來我們用 todoTypeChange 這個函式來定義 todoType 狀態的切換，這是使用按鈕點擊（帶上狀態）來觸發
3. 根據 todoType 狀態的不同，用 todo.filter 來篩選出我們需要的資料，並存在 filterTodo 這個變數中，之後在樣板中是以 filterTodo.map() 來渲染
4. 根據 todoType 狀態的不同，做 active class 的切換

```jsx
const Todo = () => {
  const [todo, setTodo] = useState([]);
  const [todoType, setTodoType] = useState("all");
  // ... 略

  // 切換：全部、待完成、已完成
  const todoTypeChange = (status) => {
    setTodoType(status);
  };

  // 根據 todoType ，決定顯示要什麼資料
  const filterTodo = todo.filter((item) => {
    if (todoType === "completed") {
      return item.status;
    }
    if (todoType === "active") {
      return !item.status;
    }
    return true;
  });

  return (
    // ... 略
    <div className="todo__category">
      <button
        className={`todo__category__list ${
          todoType === "all" ? "active" : ""
        }`}
        onClick={() => todoTypeChange("all")}
      >
        全部
      </button>
      <button
        className={`todo__category__list ${
          todoType === "active" ? "active" : ""
        }`}
        onClick={() => todoTypeChange("active")}
      >
        待完成
      </button>
      <button
        className={`todo__category__list ${
          todoType === "completed" ? "active" : ""
        }`}
        onClick={() => todoTypeChange("completed")}
      >
        已完成
      </button>
    </div>
    <ul className="todo__list">
      {filterTodo.map((item) => {
        return (
          <TodoItem
            // ...略
          />
        );
      })}
    </ul>
    // ... 略
  )
}
```

### 從 todo 分出的元件

這次有從 todo 頁面中，分出 TodoItem（todo 項目）、TodoCategory（todo 篩選）這兩個元件。
而這個段落會來提一下 TodoItem 這個元件，根據這個元件所需，我們得傳入 todo 項目的資訊（id、content、status），以及一些待辦的函式（刪除、狀態切換、編輯...等），可看下方程式碼的註解。

```jsx todo.jsx
// ... 略
import TodoItem from "../components/TodoItem";
import TodoCategory from "../components/TodoCategory";

const Todo = () => {
  const [todo, setTodo] = useState([]);
  const [todoType, setTodoType] = useState("all");
  const [input, setInput] = useState("");
  const [nickname, setNickname] = useState("");
  const [editTarget, setEditTarget] = useState({});

  // ... 略

  // 刪除項目
  const deleteTodo = (id) => {
    // ... 略
  };

  // 切換狀態（是否完成）
  const toggleTodo = (id) => {
    // ... 略
  };

  // 編輯項目
  const updateTodo = (id, content) => {
    // ... 略
  };

  // 切換：全部、待完成、已完成
  const todoTypeChange = (status) => {
    setTodoType(status);
  };

  // 根據 todoType ，決定顯示要什麼資料
  const filterTodo = todo.filter((item) => {
    if (todoType === "completed") {
      return item.status;
    }
    if (todoType === "active") {
      return !item.status;
    }
    return true;
  });


  return (
    // ... 略
    <div className="todo__content">
      <TodoCategory
        todoType={todoType}
        todoTypeChange={todoTypeChange}
      />

      <ul className="todo__list">
        {filterTodo.map((item) => {
          return (
            <TodoItem
              key={item.id} // id
              id={item.id} // id
              content={item.content} // input text
              status={item.status} // 狀態，預設為待完成
              toggleTodo={toggleTodo} // 狀態切換的函式
              deleteTodo={deleteTodo} // 刪除項目的函式
              editTarget={editTarget} // 當前編輯的暫存物件 （存 id、content)
              setEditTarget={setEditTarget} // 當前編輯的暫存物件
              updateTodo={updateTodo} // 編輯項目的函式
            />
          );
        })}
      </ul>
    </div>
  );
};
export default Todo;

```


接下來是 TodoItem.jsx 這個元件本身，要使用透過父層（props）傳進來的資料或函式時，需先引用 propTypes 來做定義。

再來會提到 editTarget 跟 setEditTarget 的部分，在定義上是用來存取暫時資料，方便用於程式上的判斷。當我們初始去點擊編輯 icon 時，就會觸發 `setEditTarget({ id, content })`，並將項目的 id 跟 content(input) 給傳入做暫時資料。
而這樣的暫時資料，可以在畫面使用 `editTarget.id === id` 這樣的方式，來判斷我們點到的是哪一個項目，並顯示相對應的內容。例如我們點到第二個項目，對於這個項目就會顯示一個 input 框，以及一個 check icon，而當我們點擊 check icon 時，就會觸發 updateTodo 的功能，並將 id 跟新的 content(input) 帶進去執行編輯的 api。

```jsx TodoItem.jsx
import propTypes from "prop-types";

const TodoItem = ({
  id,
  content,
  status,
  toggleTodo,
  deleteTodo,
  editTarget,
  setEditTarget,
  updateTodo,
}) => {
  const atChange = () => {
    toggleTodo(id);
  };
  const atClickDelete = () => {
    deleteTodo(id);
  };
  const atClickEdit = (id, content) => {
    updateTodo(id, content);
  };

  return (
    <li className="todo__item">
      <label className="todo__label" htmlFor={id}>
        <input
          className="todo__check"
          type="checkbox"
          id={id}
          checked={status}
          onChange={atChange}
        />
        {editTarget.id === id ? (
          <input
            className="form__input"
            type="text"
            value={editTarget.content}
            onChange={(e) => {
              setEditTarget({ ...editTarget, content: e.target.value });
            }}
          />
        ) : (
          <span className="todo__text">{content}</span>
        )}
      </label>
      <button type="button" className="todo__icon">
        {editTarget.id === id ? (
          <i
            className="fa-solid fa-check"
            onClick={() => atClickEdit(editTarget.id, editTarget.content)}
          ></i>
        ) : (
          <i
            className="fa-regular fa-pen-to-square"
            onClick={() => {
              setEditTarget({ id, content });
            }}
          ></i>
        )}
      </button>
      <button type="button" className="todo__icon" onClick={atClickDelete}>
        <i className="fa fa-times" />
      </button>
    </li>
  );
};

TodoItem.propTypes = {
  id: propTypes.string.isRequired,
  content: propTypes.string.isRequired,
  status: propTypes.bool.isRequired,
  toggleTodo: propTypes.func.isRequired,
  deleteTodo: propTypes.func.isRequired,
  editTarget: propTypes.object.isRequired,
  setEditTarget: propTypes.func.isRequired,
  updateTodo: propTypes.func.isRequired,
};

export default TodoItem;

```

### "待完成"待辦的數量顯示、清除"已完成"項目功能

"待完成"待辦的數量顯示比較單純，宣告 todoUnCompleted 這個變數去儲存 todo 篩選待完成的項目，然後再用 `{todoUnCompleted.length}` 渲染到畫面上。

而清除"已完成"項目的功能，則是使用 todo 去篩選已完成項目，然後帶入這些已完成項目的 id 到 apiDeleteTodos 做刪除處理。因為要刪除的項目不只一個，所以會使用到 Promise.all 來等待全部已完成的項目都被刪除後，再用 Toast 通知使用者。
（記得 getTodos() 重新獲取資料。）

```jsx todo.jsx
import Swal from "sweetalert2";
import { useNavigate } from "react-router-dom";
import { useState, useEffect } from "react";
import {
  apiUsersCheckout,
  apiGetTodos,
  apiPostTodos,
  apiDeleteTodos,
  apiPatchTodos,
  apiPutTodos,
  todoBase,
  apiUsersSignOut,
} from "../api";
// ... 略

const Todo = () => {
  const [todo, setTodo] = useState([]);
  const [todoType, setTodoType] = useState("all");
  const [input, setInput] = useState("");
  const [nickname, setNickname] = useState("");
  const [editTarget, setEditTarget] = useState({});

  // ... 略

  // 從原先 list 資料去 filter
	const todoUnCompleted = todo.filter((item) => {
		return !item.status;
	});

  // 清除已完成項目
  const clearTodoCompleted = () => {
    const result = todo.filter((item) => {
      if (item.status) {
        apiDeleteTodos(item.id);
      }
    });
    Promise.all(result).then(() => {
      Toast.fire({
        icon: "success",
        title: "清除已完成項目成功",
      });
      getTodos();
    });
  };

  return (
    // ... 略
    <div className="todo__bottom">
      <p className="todo__bottom__text">
        {todoUnCompleted.length} 個已完成項目
      </p>
      <button
        className="todo__bottom__btn"
        onClick={clearTodoCompleted}
      >
        清除已完成項目
      </button>
    </div>
  )
}
```

### 登出

登出在執行 apiUsersSignOut() 時，記得清理 document.cookie (`document.cookie = "token=; expires=;"`)

在登出成功時，會透過 navigate 導回登入頁面

```jsx todo.jsx
import Swal from "sweetalert2";
import { useNavigate } from "react-router-dom";
import { useState, useEffect } from "react";
import {
  apiUsersCheckout,
  apiGetTodos,
  apiPostTodos,
  apiDeleteTodos,
  apiPatchTodos,
  apiPutTodos,
  todoBase,
  apiUsersSignOut,
} from "../api";
// ... 略

const Todo = () => {
  // ... 略

  const navigate = useNavigate();

  // ... 略

  // 登出
  const signOut = () => {
    apiUsersSignOut()
      .then(() => {
        document.cookie = "token=; expires=;";
        Swal.fire({
          title: "您已登出",
          text: "為您導回登入頁面...",
          icon: "success",
          showConfirmButton: false,
          timer: 1500,
        });
        setTimeout(() => {
          navigate("/");
        }, 1500);
      })
      .catch(() => {
        Swal.fire({
          title: "登出失敗，請再檢查看看",
          icon: "error",
          showConfirmButton: false,
          timer: 1500,
        });
      });
  };

  return(
    // ... 略
    <div className="greet">
      <h4 className="greet__text">Hi~ {nickname}</h4>
      <button className="greet__btn" type="button" onClick={signOut}>
        登出
      </button>
    </div>
    // ... 略
  )
}

```

----

## 結尾

透過這幾週跟著作業產出拆解文章，比較有感的部分是**狀況的掌握**，不論正在製作哪個功能，或者正卡關在什麼地方，都比較不會混亂跟一片空白。

講到一片空白，也來分享自己先前看書所學習到的。基本上來說，即使現在的科技相當發達，我們大腦主要還是以**求生**作為導向，所以當我們面臨太多資訊、思考太多時，大腦會容易處於當機狀態（避免過度耗能）。回想一下學生時期在準備考試時，是不是會突然開始整理房間呢？因為大腦判斷準備考試實在太麻煩（太耗能）了。而依循大腦這樣的特性，再來延伸說到**寫文章**跟**拆解任務**。

關於**寫文章**這件事，過去我自己也都無法維持住，所以往往寫個一陣子就會放掉；後來在一些探索下，我知道了無法維持的原因，因為會很貪心的在寫文章這件事添加很多資訊（完美主義作祟），像是要寫到什麼程度、別人怎麼看、是否容易理解...等等，可是這樣太多的**想法**、**期待**、**資訊**只會讓大腦很快的當機，初步就無法維持習慣，畢竟阻力實在太大了。

所以我們可以先**降低門檻**，以自己為出發點：對於什麼議題有興趣、什麼部分之後會用到、想要練習什麼，或者單純分享自己踩到的雷。放掉只是想像的期待，寫的好與壞其實都沒關係，畢竟還在起頭建立習慣。像是我 8 月中做的這個 [Pinia 筆記](https://www.billyji.com/2023/08/14/learn-pinia/)，雖然簡簡單單，但沒多久我在寫 Nuxt 專案時就派上用場了。如果嘗試後真的寫不下去也沒關係，也許你是透過大量練習、實戰在組織自己知識的人；也可能是在多些經驗後，發現寫筆記、Blog 對自己的重要性，**It's all ok**。

再來討論到**拆解任務**，這也是我操作後相當推薦的方法。剛開始成為前端時，每當新專案一來（或者舊專案要修改），二話不說就是直接打開編輯器開工；不過當工作累積一些經驗後，變成專案一來會先規劃跟拆解，基本上我電腦桌的附近一定會擺上筆記本或紙。而這樣的拆解在專案的進行上會比較順，也是順著大腦的特性走，讓它不會一次進太多資訊而當機。不過在實際專案上，因為有時間上的壓力，無法像上課這樣拆解的比較細，所以可以好好利用上課來嘗試拆解。

感謝你看到這邊，一不注意就在結尾處提了比較多。若有什麼部分寫錯，也再麻煩跟我說，感恩！

（感謝 Evan 同學發現待辦 CRUD 沒寫好的部分！）
（感謝助教提供優化部分！）