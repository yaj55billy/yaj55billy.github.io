---
title: TypeScript 列舉型別
date: 2024-11-17 16:22:10
tags: [JavaScript, TypeScript]
categories: [前端 TypeScript 系列]
excerpt: Enum 被稱為「列舉」或「枚舉」，用來將性質類似的選項，用物件的鍵（Key）來匯聚成一個型別，很適合用在一組固定範圍的情境。
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

當沒有對項目賦予值時，TypeScript 會幫我們去對應從 0 開始遞增的數字。而我們也能主動對項目賦予值，不過**在型別上只能是數字或字串，不能是這兩種型別以外的值**。如下方三個範例所示：

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

### 特性與需注意

當列舉型別沒有被標示值時，會有這樣的特性：查看前一個項目的值並進行 `+1` 的遞增，而如果第一個項目沒有被賦予值時，則會代入數字 0。如下方範例，`UserRole.Editor` 會參考 `UserRole.Admin` 的值，然後進行 `+1` 的遞增，所以 `UserRole.Editor` 的值為 101，而 `UserRole.Viewer` 則以此類推為 102。

```tsx
enum UserRole {
	Admin = 100,
	Editor,
	Viewer,
}
console.log(UserRole.Editor); // 101
console.log(UserRole.Viewer); // 102
```

以下方這個範例來說，`UserRole.Admin` 是第一個項目且沒有值，所以會預設代入 0 這個數字，而 `UserRole.Editor` 則是被設置為 0，不過這樣並不會報錯，甚至兩者做 `===` 比對時是相等的。因為 **TypeScript 不會檢查這些項目的值是否不合理或重複**。

```tsx
enum UserRole {
	Admin, // 第一個項目沒被賦予，則會代入 0
	Editor = 0,
	Viewer, // 參照 Editor，然後 +1
}
console.log(UserRole.Admin); // 0
console.log(UserRole.Admin === UserRole.Editor); // true
```

接下來討論字串型列舉要注意的部分，如果第一個項目（前一個項目）的值為字串型別，則後續的項目都需要被定義值，不然會出現 `Enum member must have initializer.` 這樣的錯誤。以下方註解 ❌  的範例來說，因為 `UserRole.Editor` 無法根據前一個 `'Admin'` 值做 `+1` 的遞增，所以我們會收到 TypeScript 的錯誤提醒。

```tsx
enum UserRole { // ❌
	Admin = "Admin",
	Editor, // 報錯：Enum member must have initializer.
	Viewer, // 報錯：Enum member must have initializer.
}

enum UserRole { // ✅
	Admin, // 被代入 0
	Editor, // 根據 Admin 的值 +1
	Viewer = "Viewer", // 字定義為 'Viewer'
}
```

這個需注意的段落，雖然實務上應該不會遇到（~~真的這樣寫可能會被亂棒毆打~~），但學習時有看到，還是筆記下來。

## 關於逆向映射性（反向查找）

在 TypeScript 使用列舉時，除了可以用項目（鍵）來查找值（`UserRole.Admin`），我們也可以反向用值來找查找項目名稱（`UserRole[0]`），這樣的相互映射稱為逆向映射性。

```tsx
enum UserRole {
	Admin, // 預設值 0
	Editor, // 1
	Viewer, // 2
}

console.log(UserRole.Admin); // 輸出: 0

// 逆向映射：用值查找鍵
console.log(UserRole[0]); // 輸出: "Admin"
console.log(UserRole[1]); // 輸出: "Editor"
```

如下方範例所示，透過編譯後的結果，我們可以更了解這樣的特性是從何而來，以 `UserRole[UserRole["Admin"] = 0] = "Admin";` 這整段來說：

- `UserRole["Admin"] = 0`：這部分將 `UserRole` 列舉的項目 `Admin` 設置為 0，而這整體又是一個表達式，最終會回傳數值 0。
- `UserRole[0] = "Admin"` ：接著我們將上方 `UserRole["Admin"] = 0` 整體回傳的 0 給代入，`UserRole[0]` 被設置為 `"Admin"` 這個值。

```tsx
enum UserRole {
    Admin,  // 預設值 0
    Editor, // 1
    Viewer  // 2
}

⬇️⬇️⬇️⬇️⬇️⬇️  //編譯

var UserRole;
(function (UserRole) {
    UserRole[UserRole["Admin"] = 0] = "Admin";
    UserRole[UserRole["Editor"] = 1] = "Editor";
    UserRole[UserRole["Viewer"] = 2] = "Viewer";
})(UserRole || (UserRole = {}));
```

不過在有些狀況，就不會有逆向映射的特性：

- 在字串型列舉型別的項目名稱與值不一樣時（以下範例說明）
- 使用常數列舉型別時（下一段將會說明）

在下方兩個字串型列舉型別的範例中，我們可以了解到：**字串型列舉的編譯結果，是沒有雙向設置的部分**。不過在項目名稱（鍵）跟值一樣時，因為可以將值反向作為屬性參照，所以仍然保有逆向映射的特性。

而以第二個範例來說，在項目名稱（鍵）跟值不同時，我們就無法反向來查找了。（`UserRole["Abc"]` 無法查找出 `"Admin"`）

```tsx
// ✅
enum UserRole {
    Admin = 'Admin',
    Editor = 'Editor',
    Viewer = 'Viewer'
}

⬇️⬇️⬇️⬇️⬇️⬇️ // 編譯

(function (UserRole) {
    UserRole["Admin"] = "Admin";
    UserRole["Editor"] = "Editor";
    UserRole["Viewer"] = "Viewer";
})(UserRole || (UserRole = {}));
```

```tsx
// ❌
enum UserRole {
    Admin = 'Abc',
    Editor = 'Efg',
    Viewer = 'Victor'
}

⬇️⬇️⬇️⬇️⬇️⬇️ // 編譯

var UserRole;
(function (UserRole) {
    UserRole["Admin"] = "Abc";
    UserRole["Editor"] = "Efg";
    UserRole["Viewer"] = "Victor";
})(UserRole || (UserRole = {}));
```

## 常數列舉型別

內容...

參考資料：

- 六角學院 TypeScript 30 天課程
- 書籍：[<讓 TypeScript 成為你全端開發的 ACE！>](https://www.tenlong.com.tw/products/9789864344895?list_name=srh)
- [在大腦升級後，我想學 TypeScript 了 Day 11 - 列舉 Enum Type](https://www.youtube.com/watch?v=KbG-n9Eo1C8&list=PLAau7VdvlBLrzob4OceqD2aoQ5FQMXg90&index=10)

---

Photo by <a href="https://unsplash.com/@safarslife?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Safar Safarov</a> on <a href="https://unsplash.com/photos/turned-on-gray-laptop-computer-MSN8TFhJ0is?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
