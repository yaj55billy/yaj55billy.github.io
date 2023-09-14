---
title: CSS FLEX (補債)
date: 
tags:
  - 前端
  - CSS
  - FLEX
categories:
  - 前端
description: 
cover: https://i.imgur.com/BkwBMOG.png
---

## 前言

CSS 排版的演進史：table > float > inline-block > flex > grid

補債，

演進史差不多是這樣，之前因為需考慮到 IE(9~11) ，所以我一直都是用 float 跟 inline-block，以避免掉無謂的跑版。


----

## 指令
```html
<div class="container">
  <div class="item">1</div>
  <div class="item">2</div>
  <div class="item">3</div>
</div>
```
```css
.container {
  display: flex;
}
```
在使用 FLEX 時，有**外容器**跟**內容項目**這兩個部分。而使用的起手式就是在外容器打上`display: flex;`（上方案例為 container），接著裡頭的項目（案例為 item）就會變成是 FLEX 的內容項目。簡單來說，我們就可以開始在外容器跟內容項做 FLEX 相關的指令操作。

接著來看外容器與內容項目可以用的指令：
**外容器**
- display
  - flex（起手式）
  - inline-flex（較少使用）
- flex-direction（主軸的方向，內容項目會依據這個方向設置排列） 
  - **row**：為預設值，內容項目會依照主軸**從左至右**的橫向排列
  - row-reverse：內容項目會依照主軸**從右至左**的橫向排列，而項目也會依序從右到左
  - column：內容項目會依照主軸**從上至下**的直向排列
  - column-reverse：內容項目會依照主軸**從下至上**的直向排列，而項目也會依序從下到上
- flex-wrap（是否換行）
  - **nowrap**：為預設值，內容項目不會換行（如果內容項目太多時，畫面會壞掉）
  - wrap：換行設置
  - wrap-reverse
- justify-content（主軸對齊設置，而這個主軸會依據 flex-direction 設置，稍後用青蛙來看）
  - **flex-start**：預設值，內容項目會對齊在主軸起始位置（ex: 在 flex-direction: row 的預設上，內容項目會對齊在左邊）
  - flex-end：內容項目會對齊在主軸終點位置
  - center：內容項目會對齊在主軸中間


（進度到這邊）
  - space-around：以各個 item 左右帶上距離平均分配
  - space-between：在 item 中帶上平分好的距離
  - space-evenly：沒有固定頭尾，各自帶上平分的距離

- align-items（交錯軸）
  - **stretch**
  - flex-start
  - flex-end
  - center
  - baseline
- align-content（交錯軸，多行狀況）
    - **stretch**
    - flex-start
    - flex-end
    - center
    - space-around
    - space-between
        
**內容項目**
- align-self 
  - **auto**
  - stretch：會依照內容項目中高度最高為依據，跟這個項目等高
  - flex-start：
  - flex-end
  - center
  - baseline
- order：所有內容項目都預設是 0
- flex 0 1 auto
  - flex-grow
  - flex-shrink
  - flex-basis


!重要
首先會先提一個重要的前提：關於主軸與交叉軸不要用 x,y 軸來理解，而是要以 flex-direction 設定為依據。



align-items:
stretch 這個會在項目中以最高為主，然後去拉伸其他項目
（遇到對齊狂會用到的）
base-line: 會對齊"內容"底部


子層
order: 預設為 0
所以要跑到第一個元素可設定為 -1
最後則可設為 1

flex-grow: 元素的增長
盡可能地生長，補滿剩下的空間 （扣除一些 margin）
flex-grow: 1
flex-grow: 3 3比1 大上三倍
（這個部分程式會自行去計算）
https://www.udemy.com/course/advanced-css-and-sass/learn/lecture/8274580#announcements


## 用青蛙來看需要注意什麼？

以上是文字的紀錄，會滿建議有了初步了解後，直接去玩青蛙的遊戲，
最後一關會稍微卡一下，覺得關鍵在 flex-wrap: wrap-reverse 


## FLEX 解決了？（我的個人實戰）

我想特別舉出等高的狀況：應用在 card，曾經在 card 排列上，還得去限定字數，不然有的卡片內容比較多，有的卡片內容比較少，或者是高低差，然後客戶會要求要等高時... 
過去會用上這種方式：限定字數，內容變為 ...、用 inline-block 排版使之對齊上方 ... 等高時，要寫死高度等等

----

## 結尾與參考

使用 Flex 好處：
- 之前使用 float 或 ... 有時候會比較常做計算，
- 對齊變得簡單
- 彈性、隨意控制走向
- 複雜的版面，可以不用用到太多架構，有時候為了實現比較複雜的版型，往往都會需要外加一層到兩層來處理。
- flex 父層都是能抓到子層的高度，所以不用像 float 做 clear
- ~~應付客戶天馬行空的對齊~~

          資料流的走向(主軸或交叉軸)
            X 不用 xy 軸來記這個
            看資料是橫向或直向
            資料流如果是橫向，則交叉軸是直向
            資料流如果是直向，則交叉軸就是橫向

            關於交叉軸的對齊控制:
            align-items(單行子物件)
              flex-start (靠近交叉軸的起點)
              center
              flex-end (靠近交叉軸的終點)
            align-content(多行子物件)
              space-around
              space-between



