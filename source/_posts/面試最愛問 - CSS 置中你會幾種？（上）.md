---
title: 面試最愛問 - CSS 置中你會幾種？ （上）
date: 2022-03-22 14:38:08
tags: [CSS, text-align, margin, flexbox, position, block, inline, inline-block]
categories:
- CSS
description: 相信做過許多面試考古題或者已經面試許多次的前端工程師們，多多少少都會遇過這一題，而每當被問到時，就要準備拿出自己存放已久的壓箱寶讓面試官嘖嘖稱讚~~大失所望~~。方法雖然有許多種，但其實有一些在實際應用上並不是這麼常見又或者存在著一些缺點。這裡就來帶大家瞭解實際應用上比較常使用的方法吧！
---
## 前言

相信做過許多面試考古題或者已經面試許多次的前端工程師們，多多少少都會遇過這一題，而每當被問到時，就要準備拿出自己存放已久的壓箱寶讓面試官嘖嘖稱讚~~大失所望~~。方法雖然有許多種，但其實有一些在實際應用上並不是這麼常見又或者存在著一些缺點。這裡就來帶大家瞭解實際應用上比較常使用的方法吧！

![垂直水平置中的方法你只知道一種？？](https://i.imgur.com/b8KJt08.jpg)

## 水平置中

### text-align: center

首先為大家介紹的水平置中方法是 `text-align: center`，主要用於**文字**以及 **inline element**，而具備 inline 特性的 **inline-block element** 也是同樣適用的。只要在外層加上此屬性就能夠達到如下方所示的水平置中效果。而由於 div 屬於 block element 所以並不適用此方法。

<iframe height="300" style="width: 100%;" scrolling="no" title="水平置中 text-align" src="https://codepen.io/TimmyLin/embed/yLpoabq?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/yLpoabq">
  水平置中 text-align</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

### margin: 0 auto

面對 **block element** 時，我們可以使用 `margin: 0 auto` 這個方法，但須特別注意的是這個屬性是加在想置中的元素上且須定義**元素的寬度**。在下方的例子中，我們可以看到 p 雖然已經加上了寬度，但文字還是沒有正確置中，如果想要文字置中，還是使用 `text-align: center` 會比較好。

<iframe height="300" style="width: 100%;" scrolling="no" title="水平置中 text-align" src="https://codepen.io/TimmyLin/embed/GRyvjQW?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/GRyvjQW">
  水平置中 text-align</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

### position + transform

接下來介紹一些 **inline element** 或 **block element** 都能使用的方法，第一種方法是 **position + transform** ，首先必須將元素改為相對定位 `position: relative` 使其可以根據所在的基準位置進行移動，接著定義元素距離左邊移動 50%，也就是 `left:50%`，這時看起來好像有點置中，但其實會比自身的寬度在偏移 50% 所以必須使用 transform 做調整 `transform: translateX(-50%)`，這樣就成功達成水平置中了，可喜可賀！需注意文字還是必須加上 `text-align: center` 才能達成真正的置中，詳細可以參考下方程式碼。

<iframe height="300" style="width: 100%;" scrolling="no" title="水平置中 margin: 0 auto" src="https://codepen.io/TimmyLin/embed/NWXvRZL?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/NWXvRZL">
  水平置中 margin: 0 auto</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

### flexbox

而通用的第二種方法就是使用 flexbox 的屬性做調整，首先我們需要在外層定義 `display: flex`，讓元件知道我們要用 flexbox 的形式做排列，之後定義 `justify-content: center` 使主軸的元素們能夠置中對齊。需注意的是 flexbox 會使內層的屬性如 inline-block 排列，因此若需要元素佔據一整列需搭配 `width: 100%` 與 `flex-wrap:wrap` 做調整，詳細可以參考以下程式碼。

<iframe height="300" style="width: 100%;" scrolling="no" title="水平置中 position + transform" src="https://codepen.io/TimmyLin/embed/BaJdQME?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/BaJdQME">
  水平置中 position + transform</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

## 結語

礙於篇幅的關係，以上只提到水平置中的方法，關於垂直置中的方法，將會在後續的文章繼續為大家做介紹，那我們就下次見囉！
