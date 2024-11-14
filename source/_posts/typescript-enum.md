---
title: TypeScript 列舉型別
date: 2024-11-12 16:22:10
tags: [JavaScript, TypeScript]
categories: [前端 TypeScript 系列]
excerpt: TypeScript 列舉型別
index_img: "/img/typescript.jpg"
banner_img: "/img/article-banner.jpg"
---

## 什麼是 Enum

Enum 被稱為「列舉」或「枚舉」，在原生的 JavaScript 並沒有這樣的概念，而是在 TypeScript 才新增的型別概念。以下內容將統一用列舉稱呼，它用來**將性質類似的選項，用物件的鍵（Key）來匯聚成一個型別**，很適合用在一組**固定範圍**的情境。例如：

- 訂單狀態：待處理、運送中、已完全
- 權限管理：管理者、可編輯者、僅能閱覽者
- 表單驗證：有效、無效、驗證中

如果只看說明的話，可能會有些抽象，所以我們用「角色權限管理」作為範例來做說明：

```tsx
// 定義角色權限列舉
enum UserRole {
	Admin, // 管理員
	Editor, // 可編輯者
	Viewer, // 僅能閱覽者
}

// 空的用戶列表
let users: { username: string; role: UserRole }[] = [];

// 新增用戶 Fn
function createUser(username: string, role: UserRole) {
	users.push({
		username: username,
		role: role,
	});
}

// 新增用戶角色
createUser("Alice", UserRole.Admin);
createUser("Bob", UserRole.Editor);

// 顯示用戶列表
console.log(users);
```

如上方範例所示，我們可以透過 `enum` 這樣的關鍵字來做定義，而名稱依循命名慣例為首字大寫（`UserRole`），而定義後可讓變數、參數做型別註記。這樣的方式就像為變數、參數設置了一個「選項清單」，所以得依循這個清單的選項，就像我們到餐廳用餐時，只能依循這家餐廳的菜單點菜，而不能點其他菜單上沒有的品項。

使用列舉能讓我們**更有組織來管理**性質類似的項目，而在擴充時也能較為便利。
