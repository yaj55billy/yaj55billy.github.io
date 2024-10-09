---
title: 什麼是資料庫？
date: 2024-10-09 10:26:29
tags: [資料庫, 前端到後端系列]
categories: [前端]
excerpt: 資料庫
index_img: "/img/typescript.jpg"
# banner_img: "/img/typescript.jpg"
---

## 資料庫的概念與應用

> **資料庫**（英語：database），簡而言之可視為[數位化](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E5%8C%96)的[檔案櫃](https://zh.wikipedia.org/wiki/%E6%A1%A3%E6%A1%88%E6%9F%9C)——儲存電子[檔案](https://zh.wikipedia.org/wiki/%E6%AA%94%E6%A1%88)的處所，使用者可以對[檔案](https://zh.wikipedia.org/wiki/%E6%AA%94%E6%A1%88)中的資料執行新增、擷取、更新、刪除等操作[[1]](https://zh.wikipedia.org/zh-tw/%E6%95%B0%E6%8D%AE%E5%BA%93#cite_note-1)。
> 所謂「資料庫」是以**一定方式**儲存在一起、能予多個使用者[共享](https://zh.wikipedia.org/wiki/%E5%85%B1%E4%BA%AB)、具有儘可能小的[冗餘度](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%86%97%E4%BD%99)、與應用程式彼此獨立的資料[集合](<https://zh.wikipedia.org/wiki/%E9%9B%86%E5%90%88_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)>)。一個資料庫由多個表空間（[Tablespace](https://zh.wikipedia.org/wiki/Tablespace)）構成。

以上是維基百科對於資料庫（Database）的定義。
比較簡單來說，資料庫是一個專門儲存資料的地方，這些資料可以是文字、圖片、數據等，而我們也可以透過一些方式，對這些資料進行讀取、新增、更新和刪除的操作。

我們可以從生活常見的情境來說，不論是在瀏覽社交平台的訊息，或者在使用電商網站購物時，這些過程看到的許多資料，像是會員操作、好友動態、購物訂單等，這些操作的背後都有資料庫在默默地工作（儲存、操作資料），所以沒有資料庫的話，這些功能將無法順利運作。

## 資料庫管理系統（DBMS）與資料庫

在上個段落中，我們了解到資料庫是資料儲存的地方。而想要有效率地管理、查詢和操作這些資料，我們就會需要資料庫管理系統（Database Management，DBMS）來協助。

![資料庫管理系統(DBMS)與資料庫譬喻](/img/what-database-1.png)

如上圖所示，我們可以把資料庫管理系統跟資料庫做個譬喻，將資料庫想像為一個「圖書館」，而資料庫管理系統就如同「圖書館的管理員」。

圖書館這個空間中，儲存了許多書籍（資料），但還需要有圖書館管理員（系統）來協助我們查找書籍、借書還書、管理書籍的位置等。比如說，當借閱者想找某本書，我們只需詢問圖書館管理員，他就能幫我們找出這本書在哪個書架。

## 關聯式資料庫（Relational Database）與非關聯式資料庫（Non-Relational Database）

資料庫的類型會根據使用情境不同而有所區分，最常見的分類有：關聯式資料庫與非關聯式資料庫，而接下來將會分別討論這兩者。

### 關聯式資料庫（Relational Database）

在關聯式資料庫中，是以表格（table）為基礎來組織跟管理資料的，每個表格由欄位（columns）和資料列（rows）組成，欄位定義了資料的屬性，而每一個資料列則代表一筆紀錄，在表格與表格間可以透過鍵值來建立**關聯**。常見的關聯式資料庫系統有：MySQL、PostgreSQL、SQL Server 等。

關聯式資料庫特點：

- 歷史悠久且穩定：關聯式資料庫從 1970 年被提出並開始發展，而經過多年的發展跟廣泛運用，它的架構已相當成熟且穩定。
- 結構化資料（嚴謹）：資料以結構化的方式儲存，每一筆資料都必須符合事先定義好的欄位規範（Schema）。
- 使用 SQL 操作：關聯式資料庫主要使用 SQL（Structured Query Language）進行資料的操作，SQL 語言是一個標準化的查詢語言，在不同的關聯式資料庫系統是通用的。
- 彈性較低：由於資料結構事先被嚴謹定義，所以當資料需求發生變動時（例如添加或修改欄位），需要考慮的因素比較多，像是會不會影響到現有資料或其他關聯的資料表等。

---

參考資料：

- [維基百科](https://zh.wikipedia.org/zh-tw/%E6%95%B0%E6%8D%AE%E5%BA%93)
- [Node.js 不負責系列 Day9 - What is a database？](https://ithelp.ithome.com.tw/m/articles/10318313)
- [mysql.tw 資料庫 vs 資料庫管理系統](https://arc.net/l/quote/zxcgbzcm)
- [【資料庫】SQL 3 小時初學者教學](https://www.youtube.com/watch?v=gvRXjsrpCHw&t=2229s&ab_channel=GrandmaCan-%E6%88%91%E9%98%BF%E5%AC%A4%E9%83%BD%E6%9C%83)
- [宇先程式 35min 簡單認識 SQL & NoSQL 資料庫](https://www.youtube.com/watch?v=AbDh-ky8pbk&ab_channel=%E5%AE%87%E5%85%88%E7%A8%8B%E5%BC%8F)
