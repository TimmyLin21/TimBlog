---
title: 不一樣的排版方法 - CSS Grid
date: 2022-04-05 14:18:23
tags: [CSS, Grid]
categories:
- CSS
description: 隨著瀏覽器的支援度越來越高， CSS Grid 成為除了 flexbox 之外的另一種排版方法，根據 State of CSS 2021 的市調， CSS Grid 的使用率從 2019 年的 54.8% 慢慢增加到 2021年的 83.1%，由此可知學會 CSS Grid 的重要性是多麽地高，因此就讓我們來瞭解如何使用 CSS Grid 吧！
---
## 前言

隨著瀏覽器的支援度越來越高， CSS Grid 成為除了 flexbox 之外的另一種排版方法，根據 [State of CSS 2021](https://2021.stateofcss.com/en-US/features/layout/#grid) 的市調， CSS Grid 的使用率從 2019 年的 54.8% 慢慢增加到 2021年的 83.1%，由此可知學會 CSS Grid 的重要性是多麽地高，因此就讓我們來瞭解如何使用 CSS Grid 吧！

![你看，又有新的排版方法！](https://i.imgur.com/4ScPry7.jpg)

## 基本結構

CSS grid 就如同他的名稱一樣，是以格狀系統去規劃版面的結構。首先，我們會先在外層定義 grid-template，也就是這個格線系統的樣板，接著再賦予內層的元件 grid-area，也就是其所佔的空間。這種棋盤式的規劃，相較於 flexbox 更能準確地定義元件的位置。以下為簡易示範，將格線系統定為 3x3 的結構，並給予內部的元素從左上至右下各佔據一個格子。

<iframe height="300" style="width: 100%;" scrolling="no" title="Grid template 1" src="https://codepen.io/TimmyLin/embed/WNddVwb?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/WNddVwb">
  Grid template 1</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

## Grid template

知道 CSS Grid 的基本架構後，讓我們來瞭解如何定義外層的 grid-template。首先，grid-template 又可以分成 grid-template-columns 和 grid-template-rows，兩者分別是用來定義格線系統的行與列該如何切分。切分的方式可以直接指定一個大小，如: %, em, rem 或者 px。除此之外，也可以按照比例做切分，每一個比例的大小為 1fr (fractional)。而 CSS Grid 也很貼心的為我們準備了類似函式的功能 `repeat()`，只要需入次數與切分的大小，如：`repeat(5, 20%)`，就能創造出相同大小的網格，讓我們能夠節省重複輸入的時間，是不是很方便啊！以下的範例改寫了第一個範例，將 grid-template 拆分成 grid-template-columns 和 grid-template-rows，另外也將 `repeat(3, 1fr)` 展開成ㄧ般的寫法，讓大家瞭解 `repeat()` 是如何運作的。

<iframe height="300" style="width: 100%;" scrolling="no" title="Grid template 1" src="https://codepen.io/TimmyLin/embed/KKZZOWz?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/KKZZOWz">
  Grid template 1</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

## Grid Area

接著我們來認識一下在 CSS Grid 中如何定義內部的元件。同樣地， grid-area 也能夠被拆分，不過這次拆分出來的不只是 column 和 row 而是 grid-row-start、grid-column-start、grid-row-end 和 grid-column-end，他們分別代表元素在格線系統中行與列的起始點以及行與列的終點。而其中輸入的值如果為正值，表示計算的起始點，行爲最上方開始，列則為最左方開始。而負值則完全相反，行從最下方開始計算，列則為最右方開始計算。但有一個比較特別的點是起始點不一定要大於終點，舉例來說： `grid-row-start: 5; grid-row-end: 3;`，這樣的設定是完全沒問題的，不過應該也不會刻意去找自己麻煩就是了。相同地，grid-area 也提供了快速的方法去幫助大家設定，只要先定義格線系統的起始點或終點後，另一側的屬性可以使用 span 搭配想要佔據的格數就能快速設定 grid-area，如： `grid-row-start: 1; grid-row-end: span 5;` 表示元素所佔據的格數為第一格到第五格。以下的範例同樣是將第一個範例修改成拆分後的結果。

<iframe height="300" style="width: 100%;" scrolling="no" title="Grid template 2" src="https://codepen.io/TimmyLin/embed/mdpXJmV?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/mdpXJmV">
  Grid template 2</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

### 結語

以上就是對 CSS Grid 的基本介紹，如果想要了解更多可以查看 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout) 文件或者挑戰這款免費的遊戲 [Grid Garden](https://cssgridgarden.com/)，相信各位在遊玩後對 Grid 會更印象深刻，那我們就下次見囉！
