---
title: 排版時不得不知的 Box Model
date: 2022-03-21 11:07:34
tags: [CSS, Box Model]
description: 不知道各位在一開始接觸 CSS 時，有沒有類似的經驗。當拿到設計稿時，明明照著設計稿的尺寸去設定，但是呈現的排版總是不如人意，後來才發現原來自己對於 Box Model 並沒有充分的瞭解導致 margin, padding 用反了，或者沒有考慮到 border 的尺寸。而這些情境都凸顯了熟悉 Box Model 的重要性。就讓我們一起來瞭解 Box Model 吧！
---
## 前言

不知道各位在一開始接觸 CSS 時，有沒有類似的經驗。當拿到設計稿時，明明照著設計稿的尺寸去設定，但是呈現的排版總是不如人意，後來才發現原來自己對於 Box Model 並沒有充分的瞭解導致 margin, padding 用反了，或者沒有考慮到 border 的尺寸。而這些情境都凸顯了熟悉 Box Model 的重要性。就讓我們一起來瞭解 Box Model 吧！

![當你還不懂 Box Model 時](https://i.imgur.com/jEXeq5b.jpg)

## Box Model

![Box Model](https://i.imgur.com/5GO105Z.png)
要知道什麼是 Box Model，首先你可以將每個 HTML 元素試想成一個一層層包覆的盒子，如同上面的圖示。以下會一一介紹每一層的作用。

### Content

Content (藍色區域) 主要是元素的內容，包含了文字與影像。不調整任何 CSS 的情況下，在開發者工具裡的 Computed 會呈現如圖片所示 auto＊auto 的字樣，表示內容區域會自動伸展他的寬度以及高度。

### Padding

Padding 用於為主要內容在周圍的區域保留空間，可以把它想像成在使用 word 時為段落文字做縮排的效果。而 Padding 的區域是透明的，若將 HTML 元素設定背景顏色，Padding 的區域也會具有相同的背景顏色。

### Border

在 Padding 與 Margin 中間還有一層 Border，也就是平常看到的框線，在剛接觸排版時請記得留意 Border 的寬度，因為寬度較小所以很常被忽略。

### Margin

Margin 主要的用途是與相鄰的 HTML 元素保持距離，與 Padding 一樣都是透明地，但是在為 HTML 元素設定背景顏色時，Margin 並不會吃到設定，在排版時需要多加留意。

## 結語

Padding 與 Margin 算是在剛接觸 CSS 時容易搞混的設定，因為兩者在視覺上都能夠讓 HTML 元素拉開空間，但當加入背景顏色或者 Border 後兩者的差異就會一目瞭然。相信大家在初步瞭接 Box Model 後應該不會再犯下相同的錯誤。之後會再介紹排版時該如何計算元素的寬度與高度，那我們就下次見囉！
