---
title: 什麼是資料庫？
date: 2024-10-12 16:08:29
tags: [資料庫, 關聯式資料庫, 非關聯式資料庫]
categories: [前端到後端系列]
excerpt: 不論是在瀏覽社交平台的訊息，或者在使用電商網站購物時，這些過程看到的許多資料，像是會員操作、好友動態、購物訂單等，這些操作的背後都有資料庫在默默地工作...
index_img: "/img/database.jpg"
banner_img: "/img/article-banner.jpg"
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
- 結構化資料：資料以結構化的方式儲存，每一筆資料都必須符合事先定義好的欄位規範（Schema）。
- 使用 SQL 操作：關聯式資料庫主要使用 SQL（Structured Query Language）進行資料的操作，SQL 是一個標準化的查詢語言，在不同的關聯式資料庫系統是通用的。
- 彈性較低：由於資料結構事先被嚴謹定義，所以當資料需求發生變動時（例如添加或修改欄位），需要考慮的因素比較多，像是會不會影響到現有資料或其他關聯的資料表等。
- 強一致性：關聯式資料庫強調資料的一致性，任何變動都會即時反應在資料庫中，我們能看到最新、最正確的資訊。以網購下訂單來做譬喻，當我們下訂單後查詢訂單狀態時，會看到的就是剛剛下的訂單，不會有延遲或錯誤。

關聯式資料庫的表格示意：類似於下方這樣的表格結構跟資料，會在關聯式資料庫中不斷看到。

| username | email               | created_at(資料創建時間) |
| -------- | ------------------- | ------------------------ |
| Alice    | alice@example.com   | 2024-10-11 10:15:30      |
| Bob      | bob@example.com     | 2024-10-10 14:45:00      |
| Charlie  | charlie@example.com | 2024-10-09 09:30:15      |

### 非關聯式資料庫（Non-Relational Database）

非關聯式資料庫又稱為 NoSQL（Not Only SQL）資料庫，它在資料的儲存是比較靈活簡單的，我們可以直接對 NoSQL 寫入資料，而不需要先定義好欄位，也適合處理大量而且多樣化的資料。常見的非關聯式資料庫系統有：MongoDB、Cassandra、Redis 等。

非關聯式資料庫的特點：

- 靈活彈性處理資料：非關聯式資料庫在設計上，可以不用事先定義結構，因此在需要擴展或修改資料時，會更為靈活跟彈性。
- 不同的資料操作方式：在關聯式資料庫的操作上，是使用 SQL 語法。而非關聯式資料庫，會根據不同的資料庫類型，而有不同的操作方式，像是 Document 形式、Key value、圖形 Graph 等等。
- 高度擴展性：非關聯式資料庫適合處理結構不固定的大數據，能夠輕鬆地進行水平擴展，在多個伺服器之間分散儲存資料。當資料需求發生變動時，無需像關聯式資料庫那樣重新設計表格，擴展相對簡單。
- 弱一致性：在非關聯式資料庫中，資料更新並不會立即同步到所有節點（伺服器），而是會在一段時間後達到資料的一致，這樣的方式可避免每次有資料寫入時，都需要等待所有節點同步，確保操作速度更快。

非關聯式資料庫示意：
這邊以 MongoDB 這個系統來做示意，它是以 Document 格式儲存，如下範例所示，我們在第一筆資料中沒有 `profile_pic` 的欄位跟資料，可是在第二筆資料中，又可因應需求而加上，不需被事先定義給限制，這種彈性在非關聯式資料庫中是很常見的。

```json
{
  "_id": "64f9b08b6aee",
  "username": "Alice",
  "email": "alice@example.com",
  "created_at": "2024-10-11T10:15:30Z"
}
{
  "_id": "64f9b08b7ffe",
  "username": "Bob",
  "email": "bob@example.com",
  "created_at": "2024-10-10T14:45:00Z",
  "profile_pic": "bob_pic.jpg"
}

```

### 關聯式資料庫與非關聯式資料庫（總結）

關聯式資料庫以表格儲存結構化資料，使用 SQL 語法操作，適合需要高一致性與嚴謹資料結構的情境，如**金融交易、電子商務**等。而非關聯式資料庫無需事先定義結構，具有彈性、高擴展跟較快的讀寫能力，適合處理大筆且多變的資料情境，如**社群媒體、即時通訊**等。

關聯式資料庫與非關聯式資料庫有各自的特點跟適合場景，我們可以根據需求而做選擇。有些特殊狀況中，也會在專案同時使用這兩種資料庫。

## 結語（感想）

幾年前在工作需求上，曾經小小接觸過關聯式資料庫系統 MySQL，不過需求是比較簡單的，單純將使用者表單資料給送到資料庫中，所以對於資料庫的很多觀念是不熟悉的，那時也沒有去補起來（~~技術債~~）。這個前端到後端的系列，想再多學習一些後端、資料庫的知識，並且做個紀錄。因年初到年中這段時間，有在六角學院 Node 入門班接觸到非關聯式 MongoDB；所以這個系列會先以關聯式資料庫出發，而這個系列第二篇的預期內容是：使用 SQL 來操作資料的 CRUD。

本來寫這篇文章時，有個預期是想把蒐集到的資訊，用更簡單口語的方式表達出來，不過目前功力還不太夠。透過這樣的刻意學習，確實有讓自己多看懂，試著接觸一些本來不太想看的資訊，也算是收穫滿滿哩 XD...

主要參考資料：

- [維基百科](https://zh.wikipedia.org/zh-tw/%E6%95%B0%E6%8D%AE%E5%BA%93)
- [Ray - Node.js 不負責系列 Day9 What is a database？](https://ithelp.ithome.com.tw/m/articles/10318313)
- [mysql.tw 資料庫 vs 資料庫管理系統](https://arc.net/l/quote/zxcgbzcm)
- [圖解程式教學 Sam Tsai - NoSQL vs RDS 資料庫哪個適合你？](https://www.youtube.com/watch?v=S8AiU1GZByE&ab_channel=%E5%9C%96%E8%A7%A3%E7%A8%8B%E5%BC%8F%E6%95%99%E5%AD%B8SamTsai)
- [宇先程式 - 簡單認識 SQL & NoSQL 資料庫](https://www.youtube.com/watch?v=AbDh-ky8pbk&ab_channel=%E5%AE%87%E5%85%88%E7%A8%8B%E5%BC%8F)

---

Photo by <a href="https://unsplash.com/@safarslife?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Safar Safarov</a> on <a href="https://unsplash.com/photos/turned-on-gray-laptop-computer-MSN8TFhJ0is?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
