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

![SQL 與資料庫管理系統(DBMS)的關係](/img/sql-crud-1.png)

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

在不同的關聯式資料庫系統是通用的。

參考 w3schools 可以看到 SQL 跟相關語法
[SQL w3schools](https://www.w3schools.com/sql/default.asp)

---

**CREATE and INSERT**

我們要創一個 users 的資料表，() 裏頭有欄位名稱、資料型別、其他資訊

將資料（VALUES(...)）新增到 users 這個資料表

```sql

CREATE TABLE users (
  id INT NOT NULL,
  username VARCHAR(50) NOT NULL,
  email VARCHAR(100) NOT NULL,
  PRIMARY KEY (id) // 最後一個不用有逗號，不然會出錯
);

INSERT INTO users (id, username, email) VALUES (1, 'Alice', 'alice@example.com');

// 如果資料的值都有照順序給予，可以縮寫
INSERT INTO users VALUES (1, 'Alice', 'alice@example.com');

// 可讀性，但要記得有空格
INSERT INTO users (id, username, email)
VALUES (1, 'Alice', 'alice@example.com');

// 可一次新增多筆
INSERT INTO users (id, username, email) VALUES
(1, 'Alice', 'alice@example.com'),
(2, 'Bob', 'bob@example.com'),
(3, 'Charlie', 'charlie@example.com');

// 其他，值要以單引號，使用雙引號會出錯

```

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
