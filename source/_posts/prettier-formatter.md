---
layout:
title: Prettier Formatter
date: 2025-09-09 11:21:40
tags: [VSCode, Prettier]
categories: [前端]
excerpt: 使用 Prettier 這個工具，能協助程式碼撰寫時有一致的風格，也讓我們比較容易看到一些錯誤...
index_img: '/img/prettier-formatter/logo.webp'
banner_img: '/img/prettier-formatter/logo.webp'
---

## 前言

使用 [Prettier](https://prettier.io/) 這個工具，能協助程式碼撰寫時有一致的風格，也讓我們比較容易看到一些錯誤。這邊以初學 HTML 來做舉例，初學時遇到結構複雜的狀況時，難免會漏了一些結構或不小心多寫結構（例如少了兩個 `</div>`），而這時透過 Prettier 來整理（格式化）程式碼後，通常會比較容易看到類似錯誤。

## VSCode 設置 Prettier

首先我們要先在 VSCode 下載 Prettier 這個套件：
![VSCode 下載 Prettier](/img/prettier-formatter/1.png)

接著進行設置，步驟如圖所示：

1. 點選左下角齒輪 > 設定
2. 在搜尋框輸入 Default Formatter
3. 選取 Prettier - Code formatter 選項

![設置 Prettier 流程](/img/prettier-formatter/2.png)

補充：
VSCode FormatOnSave 設置記得需要勾選起來，這樣儲存檔案時才能去觸發 Prettier
![VSCode FormatOnSave](/img/prettier-formatter/3.png)

這時撰寫 HTML、CSS、SCSS、JavaScript......等程式碼，在存檔時就會幫我們做格式整理摟～

## Prettier Options

關於細節設置，可在參考 [Prettier Options 文件](https://prettier.io/docs/options)。
這邊以 JavaScript 引號來做設置舉例，當我們撰寫字串存檔時，預設會幫我們整理為雙引號`""`的格式；而如果我們想在存檔時，將字串轉換為單引號`''`，則可在 VSCode 設定搜尋 Prettier Single Quote，並如下圖做勾選。
這樣在存檔時就會將字串轉換為單引號（ex：`let str = "這是一段字串";` 會轉換為 `let str = '這是一段字串';`）

![Prettier Single Quote](/img/prettier-formatter/4.png)
