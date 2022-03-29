---
title: 計算元素寬高你需要知道的 Box Sizing
date: 2022-03-21 15:09:37
tags: [CSS, box-sizing, border-box]
categories:
- CSS
description: 在看完排版時不得不知的 Box Model 後，我們瞭解一個 HTML 元素是如何組成的，但此時我們仍然不知道 HTML 元素的寬度以及高度是如何計算的，若使用了錯誤的計算方式，你可能會遭到設計師無情的追殺，因此為了你的人身安全就讓我們一起來瞭解 Box Model 吧！
---
## 前言

在看完[排版時不得不知的 Box Model](/2022/03/21/排版時不得不知的-Box-Model)後，我們瞭解一個 HTML 元素是如何組成的，但此時我們仍然不知道 HTML 元素的寬度以及高度是如何計算的，若使用了錯誤的計算方式，你可能會遭到設計師無情的追殺，因此為了你的人身安全就讓我們一起來瞭解 Box Model 吧！

![錯誤的計算](https://i.imgur.com/5Ml0wo7.jpg)

## Box Sizing

<p class="codepen" data-height="435" data-default-tab="css,result" data-slug-hash="WNdxyQd" data-user="TimmyLin" style="height: 435px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/TimmyLin/pen/WNdxyQd">
  box sizing</a> by HungJengLin (<a href="https://codepen.io/TimmyLin">@TimmyLin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

先讓我們來看一段程式碼。什麼，相同的寬度和高度怎麼會差這麼多！！！<s>(這是 Bug，結案！)</s>
會造成這麼大的差異主要是 box-sizing 的設定不同導致計算的方式不一樣。

### Content-box

在系統預設的情況下， box-sizing 是設定為 content-box ，其計算寬度以及高度的方式是： `Total width/height = content(100px) + padding(20px) + border(10px)`，需注意 padding 和 border 如果左右都有設定記得都加入計算。而通常在拿到設計稿時，各個元素標注的寬度以及高度通常是 Total width/height 因此需要額外去計算 width 和 height 是多少，幸運的是 border-box 解決我們需要重複運算的煩惱。

### Border-box

假如 box-sizing 設定為 border-box 那寬度及高度的計算方式就會改為： `Total width/height(100px) = content + padding(20px) + border(10px)`。這樣在設定上是不是就方便許多了呢，只要輸入 width/height， padding 和 border 系統就會自動去調節 content 的大小，也因為這種便利性，在大部分 css reset 的設定上都會再額外加上 border-box 的屬性

```css
*,*:before,*:after {
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}
```

## 總結

相信各位在瞭解 Box Model 和 Box Sizing 後，應該能小幅度地減少被設計師追殺的命運，那我們就下次再見囉！
