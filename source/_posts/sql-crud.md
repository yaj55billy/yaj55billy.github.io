---
title: SQL(Structured Query Language) 語法
date: 2024-10-20 19:24:12
tags: [資料庫, 關聯式資料庫, SQL]
categories: [前端到後端系列]
excerpt: SQL 語法跟 CRUD 的基礎操作
index_img: "/img/database.jpg"
banner_img: "/img/article-banner.jpg"
---

## 前言與 SQL 用途

- SQL 是與關聯式資料庫系統溝通（這邊會附上圖片）
- 這篇文章主軸是先認識基礎的 SQL 語法，與我們會實際創建一個表格（新增、讀取、編輯、刪除）

會使用 https://sqliteonline.com/ 這個線上模擬來學習 SQL

## 內容

### 預計要創建的表

這個範例會有兩個資料表，分別是「users（用戶）」與「posts（貼文）」，這在網頁或社交媒體應用中非常常見

1. `users` 表

| 欄位名稱   | 資料型態     | 說明                 |
| ---------- | ------------ | -------------------- |
| id         | SERIAL       | 主鍵，唯一用戶識別碼 |
| username   | VARCHAR(100) | 用戶名稱             |
| email      | VARCHAR(100) | 用戶的電子郵件       |
| created_at | TIMESTAMP    | 用戶註冊時間         |

2. `posts` 表

| 欄位名稱   | 資料型態     | 說明                                |
| ---------- | ------------ | ----------------------------------- |
| id         | SERIAL       | 主鍵，唯一貼文識別碼                |
| user_id    | INT          | 外鍵，對應到 `users` 表的 `id` 欄位 |
| title      | VARCHAR(255) | 貼文標題                            |
| content    | TEXT         | 貼文內容                            |
| created_at | TIMESTAMP    | 貼文建立時間                        |

先以 user 表開始學習 CRUD，
然後再帶到關聯的部分。

### CREATE

### 新增（插入）用戶資料

### 查詢（Filter）

### 更新

### 刪除

### 關於關聯

## 結尾

下一篇將介紹 PostgreSQL..
