---
title: Undefined 與 null 之間的差異
date: 2022-03-29 10:42:57
tags: [JavaScript, undefined, 'null']
categories:
- JavaScript
description: Undefined和null是JavaScript基本型別(Primitives) 中的其中兩個型別，由於他們的部分性質十分相似，所以時常被人搞混。今天，就讓我們來瞧瞧其中的相似與相異處吧。
---
## 前言

Undefined和null是JavaScript**基本型別(Primitives)** 中的其中兩個型別，由於他們的部分性質十分相似，所以時常被人搞混。今天，就讓我們來瞧瞧其中的相似與相異處吧。

## 定義

首先我們可以從兩者的定義開始下手 [[1]](https://tc39.es/ecma262/multipage/overview.html#sec-null-value)
> null - 刻意使物件的值為空值時，此時的型別為null
> undefined - 當一個變數未賦予值的時候，此時的型別為undefined

undefined的部分比較好理解，就是當你宣告了一個變數，如果你不賦予它新的值，他的型別就會是undefined。除此之外，還有一個比較特殊的例子是當**物件**被刪除時，此時的變數也會是undefined。

```javascript
let a; 
console.log(a); // undefined

let b={};
delete b;
console.log(b); // undefined
```

至於null的部分則是強制賦予一個變數空值。而從定義中可以看到物件這個關鍵詞，假使我們用typeof去查詢變數型別的時候則會神奇地得到object，而這也是null特別的一點。

```javascript
let a=null;
console.log(typeof(a)); // object
```

## 真值判斷

相信目前大家已經知道undefined與null意義上不同的地方，接下來我們可以探討兩者之間有什麼相似的性質。假設你使用寬鬆相等去比較undefined與null，你會很驚訝地得到true，而造成這樣的結果是因為兩者都是false，也就是說如果用判斷式去判斷undefined或null都會得到false的答案。當然地，如果用嚴格相等去比較兩者，得到的答案就會是false。

```javascript
undefined == null;              //true
undefined === null;             // false

if(undefined){console.log(1)}; // 不會顯示１
if(null){console.log(1)};      // 不會顯示１
```

## 資料來源

以上就是undefined與null的差異，在此感謝以下的資料來源。

[1] ECMAScript 2022
[2] [Null & undefined 型態差異 - Node.js day 7](https://ithelp.ithome.com.tw/articles/10103799)
[3] [重新認識 JavaScript: Day 03 變數與資料型別](https://ithelp.ithome.com.tw/articles/10190873)
[4] [Why is (null==undefined) true in JavaScript?](https://www.quora.com/Why-is-null-undefined-true-in-JavaScript)
