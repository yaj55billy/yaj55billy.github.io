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

- 訂單狀態：待處理、運送中、已完成
- 權限管理：管理者、可編輯者、僅能閱覽者
- 表單驗證：有效、無效、驗證中

如果只看說明的話，可能會有些抽象，所以我們以「角色權限管理」作為範例來做說明：

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
```

如上方範例所示，我們可以透過 `enum` 這樣的關鍵字來做定義，名稱依循命名慣例為首字大寫（`UserRole`），而定義後可讓變數、參數做型別註記。這樣的方式就像為變數、參數設置了一個「選項清單」，所以得依循這個清單的選項，就像我們到餐廳用餐時，只能依循這家餐廳的菜單點菜，而不能點其他菜單上沒有的品項。

使用列舉能讓我們**更有組織來管理**性質類似的項目，而在擴充時也較為便利。

## Enum 列舉的值

在上個段落中，有提到列舉是將性質類似的選項，用物件的鍵（Key）來匯聚成型別，而**這些 Key 是有代表值的**。如果列舉型別在宣告而沒有賦予值時，**預設會是數字型列舉型別**，而項目與值的對應會從 0 開始遞增（0、1、2）。如下方範例：

```tsx
enum UserRole {
	Admin,
	Editor,
	Viewer,
}

console.log(UserRole.Admin); // 0
console.log(UserRole.Editor); // 1
console.log(UserRole.Viewer); // 2
```

在上個段落了解到當沒有對項目賦予值時，TypeScript 會幫我們去對應從 0 開始遞增的數字；而我們也能主動對項目賦予值，不過**在型別上只能是數字或字串，不能是這兩種型別以外的值**。如下方三個範例所示：

```tsx
// 數字型列舉型別
enum UserRole {
	Admin = 1, // 非預設的 0
	Editor = 2,
	Viewer = 3,
}

console.log(UserRole.Admin); // 1
console.log(UserRole.Editor); // 2
console.log(UserRole.Viewer); // 3
```

```tsx
// 字串型列舉型別
enum UserRole {
	Admin = "Admin",
	Editor = "Editor",
	Viewer = "Viewer",
}

console.log(UserRole.Admin); // Admin
console.log(UserRole.Editor); // Editor
console.log(UserRole.Viewer); // Viewer
```

```tsx
enum UserRole {
	Admin = true, // ❌ 布林值
	Editor = null, // ❌ null
	Viewer = { test: 222 }, // ❌ 物件
}
```

（待檢查）

### 特性與需注意

當列舉型別沒有標示值時，會有這樣的特性：查看前一個項目的值並進行 `+1` 的遞增，而如果第一個項目沒有被賦予值時，則會代入數字 0。如下方範例，`UserRole.Editor` 會參考 `UserRole.Admin` 的值，然後進行 `+1` 的遞增，所以 `UserRole.Editor` 的值為 101，而 `UserRole.Viewer` 則以此類推為 102。

```tsx
enum UserRole {
	Admin = 100,
	Editor,
	Viewer,
}
console.log(UserRole.Editor); // 101
console.log(UserRole.Viewer); // 102
```

所以依照這樣的特性，在 `UserRole.Admin`、`UserRole.Editor` 所對應的值會是一樣的，雖然是不合理的，但這樣並不會報錯，因為 TypeScript 不會檢查這些項目的值是否重複（不合理）。

```tsx
enum UserRole {
	Admin, // 第一個項目沒被賦予，則會代入 0
	Editor = 0,
	Viewer, // 參照 Editor，然後 +1
}
console.log(UserRole.Admin); // 0
console.log(UserRole.Admin === UserRole.Editor); // true
```

而如果前一個項目的值為字串型別，則以下有註解 ❌  的範例會報出 `Enum member must have initializer.` 這樣的錯誤，因為 `UserRole.Editor` 無法根據前一個 `'Admin'` 值做 `+1` 的遞增。

```tsx
enum UserRole { // ❌
	Admin = "Admin",
	Editor,
	Viewer,
}

enum UserRole { // ✅
	Admin, // 被代入 0
	Editor, // 根據 Admin 的值 +1
	Viewer = "Viewer", // 字定義為 'Viewer'
}
```

這個需注意的段落，雖然實務上應該不會遇到（~~真的這樣寫可能會被亂棒毆打~~），但學習時有看到，還是筆記下來。
