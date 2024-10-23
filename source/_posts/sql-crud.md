---
title: 使用 SQL 語法來操作資料
date: 2024-10-20 19:24:12
tags: [資料庫, 關聯式資料庫, SQL]
categories: [前端到後端系列]
excerpt: SQL 語法跟 CRUD 的基礎操作
index_img: "/img/database.jpg"
banner_img: "/img/article-banner.jpg"
---

## 前言（SQL 用途）

在這個系列的[什麼是資料庫？](https://yaj55billy.github.io/post/what-database.html)這篇文章中，有介紹到資料庫管理系統與資料庫的關係，並且知道資料庫類型分為關聯式跟非關聯式資料庫。

接下來這個系列將會先專注在關聯式資料庫，而關聯式資料庫主要是使用 SQL（Structured Query Language）這樣的語言來操作和管理資料。如下方圖片所示，我們可以了解 SQL 就如同溝通橋樑，它讓我們能夠向資料庫管理系統發送指令，然後對資料進行新增（CREATE）、讀取（READ）、編輯（UPDATE）和刪除（DELETE）等操作。（取每個英文的首字，就是網頁開發常看到的用詞 CRUD）

![SQL 與資料庫管理系統(DBMS)的關係](/img/sql-crud/1.png)

在這篇文章中，**將會使用 SQL 基礎語法來練習創建資料表（表格），並對資料做 CRUD 的操作。這邊也會先使用 [SQLite Online](https://sqliteonline.com/) 這樣的線上模擬工具來做練習。**

## SQL 語法與操作範例

SQL 的入門語法並不會太困難，因為它的語法使用了簡單的英文字詞。例如使用 `SELECT` 用來選取資料、`FROM` 指定資料來源、`CREATE` 用來創建資料表等。這些語法是很貼近其字面意思，因此在操作資料時相對容易理解每個語法的功能。

在接下來的範例中，我們將以社交媒體平台為主題，透過 SQL 語法來創建用戶（users）資料表和貼文（posts）資料表，並建立一個簡單的關聯。這兩個資料表之間的關聯，能讓我們追蹤每篇貼文的發文者（哪個用戶）。資料表範例如下：

**users 用戶資料表**：
| id | username | email |
| --- | -------- | ------------------ |
| 1 | Alice | alice@example.com |
| 2 | Bob | bob@example.com |
| 3 | Charlie | charlie@example.com |

**posts 貼文資料表**：
| id | user_id | title | content | created_at |
| --- | ------- | ------------------- | ------------------------------- | ---------------------- |
| 1 | 1 | 歡迎來到我的部落格 | 這是我的第一篇文章 | 2024-10-22 10:30:00 |
| 2 | 1 | 我的第二篇文章 | 這裡有一些新的更新 | 2024-10-22 12:00:00 |
| 3 | 2 | Bob 的自我介紹 | 哈囉，我是 Bob！ | 2024-10-22 13:45:00 |
| 4 | 3 | 對於程式設計的想法 | 程式設計既有趣又困難 | 2024-10-22 14:00:00 |

### 資料庫常見用詞

在進到 SQL 的語法學習跟操作之前，我們需了解一些在資料庫中常看到的用詞：資料庫（Database）、資料表（Table）、欄位（Column）、列（Row）。

- 資料庫：資料庫是用來儲存和管理所有資料的地方，它可以包含多個不同的資料表。可以將其**想像成 Google 試算表本身**，試算表內部可能有多張工作表，各自管理不同類型的資料。
- 資料表：資料表是由欄位（Column）和列（Row）所組成的結構，用來儲存特定的資料。可以**想像成 Google 試算表中的工作表**，每一個工作表都負責管理某類特定的資料，例如用戶資料或貼文資料。
- 欄位：資料表中的每個「欄位」代表一種類型的資料，例如在上方的 users 資料表中，欄位包含 `id`（用戶識別）、`username`（用戶名稱）、`email`（電子郵件地址）等。每個欄位定義了這張資料表中要儲存的資料類型。
- 列：資料表中的每一「列」代表一筆完整的資料記錄。每一筆資料記錄都會對應到資料表中的每一個欄位，並且儲存具體的值。以 users 資料表為例，第一筆資料的 `id` 是 `1`、`username` 是 `Alice`、`email` 則是 `alice@example.com`。

### users 資料表的 CRUD

接下來要進到重頭戲了，這邊會以 SQL 語法來操作 users 資料表的 CRUD。

首先打開前面提到的 [SQLite Online](https://sqliteonline.com/)，在面板的左方我們可以選擇要用哪個資料庫系統，這邊是選用 PostgreSQL。而程式碼輸入區塊跟 Run 按鈕是很常會使用到的。

![SQLite 操作區塊](/img/sql-crud/2.png)

**CREATE 創建 users 資料表**

我們首先要創建一個 users 資料表。在 SQL 的語法，我們可以使用 `CREATE TABLE 資料表名稱` 來建立新的資料表，並且在 `()` 去定義每個欄位的名稱以及對應的資料型別。關於欄位對應的資料型別，可以參考 [SQL Datatypes](https://www.w3schools.com/sql/sql_datatypes.asp)。

```sql
CREATE TABLE users (
	// 欄位名稱 這個欄位的資料型別
  id INT NOT NULL, // 每個欄位用逗號區隔
  username VARCHAR(50) NOT NULL,
  email VARCHAR(100) NOT NULL,
  PRIMARY KEY (id) // 最後一個不用有逗號，不然會出錯
);
```

- `id INT NOT NULL`：這個欄位表示每個 user 的唯一識別，而 `INT` 型別表示它是整數，`NOT NULL` 表示這個欄位必須有值，不能為空。
- `username VARCHAR(50) NOT NULL`：為用戶名稱的欄位，使用 `VARCHAR(50)` 來定義這個欄位可以存放最多 50 個字元的字串，同樣 `NOT NULL` 表示此欄位不能是空值。
- `email VARCHAR(100) NOT NULL`：用戶的電子郵件，使用 `VARCHAR(100)` 來允許最多 100 個字元的字串，也同樣不能為空。
- `PRIMARY KEY (id)`：指定 `id` 是這個資料表的主鍵，它就像是每一筆資料的「身分證」，這也表示它是唯一的（資料表不能有重覆的 `id`）。

相關操作如下圖，輸入程式碼後點擊 Run 按鈕，我們就能在左方看到 users 資料表了。

![新增 users 資料表](/img/sql-crud/3.png)

**INSERT 在資料表新增資料**

在上方的內容中，我們成功建立了 users 資料表，不過目前還沒有相關資料。而我們可以使用 `INSERT INTO` 語法，將資料新增到資料表中。

```sql
INSERT INTO users (id, username, email) VALUES (1, 'Alice', 'alice@example.com');

// 如果希望提高可讀性，也可以換行來呈現，但要記得保留空格
INSERT INTO users (id, username, email)
VALUES (1, 'Alice', 'alice@example.com');
```

- `INSERT INTO users (id, username, email)`：這表示我們要將資料新增到 `users` 資料表中，並且指定要填入的欄位名稱。
- `VALUES (1, 'Alice', 'alice@example.com')`：這是我們提供給指定欄位的值。`id` 為 1，`username` 是 `'Alice'`，`email` 為 `'alice@example.com'`。（這邊需注意字串的值要使用單引號，如果使用雙引號則會出錯）

如果資料表欄位跟值的順序一致，則也可以省略欄位名稱，直接使用 `INSERT INTO users VALUES (...)` 的簡化寫法。

```sql
INSERT INTO users VALUES (1, 'Alice', 'alice@example.com');
```

我們也可以一次新增多筆資料：

```sql
INSERT INTO users (id, username, email) VALUES
(1, 'Alice', 'alice@example.com'),
(2, 'Bob', 'bob@example.com'),
(3, 'Charlie', 'charlie@example.com');
```

在新增資料的操作上（程式碼跟 Run 按鈕），如果沒有出現錯誤，則我們依照下圖的步驟就可看到新增成功的資料哩！

![在 users 資料表新增資料](/img/sql-crud/4.png)

---

** Read **
Where...

**Update 更新**

** 刪除 **

### posts 資料表與關聯

`posts` 表：

| 欄位名稱   | 資料型態     | 說明                                |
| ---------- | ------------ | ----------------------------------- |
| id         | SERIAL       | 主鍵，唯一貼文識別碼                |
| user_id    | INT          | 外鍵，對應到 `users` 表的 `id` 欄位 |
| title      | VARCHAR(100) | 貼文標題                            |
| content    | TEXT         | 貼文內容                            |
| created_at | TIMESTAMP    | 貼文建立時間                        |

join 的用法

## 結尾

下一篇將介紹 PostgreSQL..

參考資料：

- [SQL w3schools](https://www.w3schools.com/sql/default.asp)
