---
title: '響應式網頁需要瞭解的 px, rem, em'
date: 2022-03-30 15:57:43
tags: [CSS, 響應式網頁, RWD]
categories:
- CSS
description: 在製作網頁時，我們需要一個標準單位來決定網頁中每個元素所佔的高度以及寬度。而我們所使用的單位就是 px, rem 和 em。 你可能會想怎麼那麼麻煩，為什麼不統一只用一種單位就好了呢？這是因為在不同的情況下，單位的使用會改變網站的維護性以及觀賞性，現在就讓我們一起來瞭解各個單位的使用時機吧！
---
## 前言

在製作網頁時，我們需要一個標準單位來決定網頁中每個元素所佔的高度以及寬度。而我們所使用的單位就是 px, rem 和 em。 你可能會想怎麼那麼麻煩，為什麼不統一只用一種單位就好了呢？這是因為在不同的情況下，單位的使用會改變網站的維護性以及觀賞性，現在就讓我們一起來瞭解各個單位的使用時機吧！

![當我面對px, rem 和 em 時](https://i.imgur.com/HWYzW28.jpg)

## px

首先，我們先來瞭解絕對單位 px，px 是 Pixel (像素)的簡稱，每 1px代表的是螢幕中一個點的大小，而在網頁中，預設的字體大小是 16px，相當於 h4 的大小，這種絕對的單位能夠幫助我們很快速的定義設計稿中每個元件所需要的寬度以及高度，但是當螢幕大小變換時，我們需要調整每個元件的尺寸，這時如果都是使用絕對單位去設定，會變得很難維護，這時我們就需要使用到相對單位 rem 或者是 em。

## rem

相對單位 rem 是根據**根元素**去做倍數成長，如以下範例所示，我們只要變更 html 的 font-size 就能夠同步更新其他使用 rem 為單位的元件尺寸。這對於在做響應式設計時非常有效率且易維護，只不過在實際應用上不需要將所有的尺寸都轉化為 rem ，只需要將螢幕尺寸變更時需要變化的元件轉化為 rem 即可。

<iframe height="300" style="width: 100%;" scrolling="no" title="px, rem , em" src="https://codepen.io/TimmyLin/embed/WNdErYK?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/WNdErYK">
  px, rem , em</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

## em

除了 rem 外，還有一個相對單位是 em，不同於 rem 的點在於尺寸相對的不是根元件，而是**外層元件**，如以下範例所示，我們可以看到 font-size 和 padding 越往內就越大，這是因為每一層都是根據外層做倍數成長。而由於比例上需要多花心思做規劃，因此在使用上需要特別留意。

<iframe height="300" style="width: 100%;" scrolling="no" title="px, rem , em" src="https://codepen.io/TimmyLin/embed/jOYLWgX?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/TimmyLin/pen/jOYLWgX">
  px, rem , em</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

## 結語

以上就是網頁單位的基本介紹，相信對於大家在實作響應式網頁是能夠提供一定幫助的，那我們就下次見囉！
