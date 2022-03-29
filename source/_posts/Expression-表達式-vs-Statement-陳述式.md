---
title: Expression (表達式) vs Statement (陳述式)
date: 2022-03-29 10:48:43
tags: [JavaScript, Expression, Statement] 
categories:
- JavaScript
description: 表達式和陳述式看似平凡無奇，但他們卻非常重要。若要比喻的話我覺得就像是水泥和鋼筋相對於一間房子，是支撐起JavaScript的基礎，如果想要更加深入瞭解JavaScript，讓他們絕對是必須深入學習的一環。假如你想要快速區分兩者差別的話，可以這麼解釋
---
## 前言

表達式和陳述式看似平凡無奇，但他們卻非常重要。若要比喻的話我覺得就像是水泥和鋼筋相對於一間房子，是支撐起JavaScript的基礎，如果想要更加深入瞭解JavaScript，讓他們絕對是必須深入學習的一環。假如你想要快速區分兩者差別的話，可以這麼解釋
> 表達式會回傳結果，陳述式不會回傳結果。

說了這麼多，你就給我看這些？？？相信大家想知道的應該不止這樣，就讓我們繼續深入兩者的差異吧！

## Expression (表達式)

簡單來說，任何的程式只要能取得值並回傳就是一個表達式，而程式的內容可以小到只有一個字元，例如輸入基本型別，就會得到相對應的基本型別。

```javascript
1             // 回傳1的結果
"Hello World" // 回傳"HellO World"的結果
```

因為可以生成值的特性，所以在JavaScript中到處都可以看到Expression的蹤影，而常見的表達式又可以分成以下幾種

### 基本型別表達式

就如同上面的例子，只要輸入任一個基本型別，都會回傳相對應的結果在console中。

```javascript
1             // 回傳1的結果
"Hello World" // 回傳"HellO World"的結果
true          // 回傳true的結果
undefined     // 回傳undefined的結果
null          // 回傳null的結果
```

### 主要表達式(Primary Expression)

主要表達式指的是在JavaScript中的一些特定關鍵字或者變數。

```javascript
let a = 1;
a    // 回傳1的結果

this // 回傳目前所在的位置的物件
```

### 運算子

常見的運算子．如賦值運算子(Assignment Operator)、算數運算子(Arithmetic Operator)、比較運算子(Comparison Operator)和邏輯運算子(Logical Operator)等，都算是表達式，因為他們都會回傳值。

```javascript
// 賦值運算子
a = 1      // 回傳1的結果

// 算數運算子
10+13      // 回傳23的結果
++a        // 回傳2的結果

// 比較運算子
10<13      // 回傳true的結果

// 邏輯運算子
true&&false //回傳false的結果
```

### Left-hand side Expression

Left-hand side Expression，簡稱LHS表達式。這個表達式是運用在賦值運算子(`=`)左邊的表達式，但不是所有表達式都能夠放在左邊，例如: `this`、`boolean`和`null`，如果想知道更多的規則可以參照[ECMAScript](https://tc39.es/ecma262/#sec-left-hand-side-expressions)。

## Statement (陳述式)

陳述式的話，它就像是一個指引告訴系統去執行特定的動作。這包含了常見的宣告變數或者函式、`if/else`和`for loop`。事實上，JavaScript就是由一連串的陳述式所組成的。
而常見的陳述式可以歸類為以下幾種

### 宣告 (declarations)

可能有人會想說宣告有告訴系統去執行什麼動作嗎？我怎麼感覺不出來。但事實上，系統是有在默默做事的。每當宣告時，系統就被告知生成一個記憶體空間，而變數和函式就會存放在這之中。

```javascript
var a;
let b;
const c=1;
```

### 條件陳述式 (conditional statements)

`if/else`和`switch`都屬於條件陳述式，只不過在條件陳述式中也可以看到一些表達式包覆在其中，例如if判斷的條件其實就是表達式。這邊要特別注意表達式的部分是不能替換成陳述式的。

```javascript
// 基本架構
if (expression){
    statement
}else{
    statement
}

// 在表達式的部分放入陳述式就會跳出error
if (var=1){
    console.log('error');
}
```

### 迴圈和跳躍 (Loops and Jumps)

迴圈陳述式包含了`for`、`for/in`和`while`，而跳躍陳述式是用來讓JavaScript的編譯器知道該跳躍到哪一個指定位置，常見的有`break`、`continue`和`return`。

## 函式表達式和函式陳述式

在函式的部分，因為宣告方法的不同又可以分成函式表達式(Function Expressions)和函式陳述式(Function Declarations)。

### 函式表達式(Function Expressions)

函式表達式是一種變數宣告的表達式，意思是說會宣告一個變數，然後將函式賦予給這個變數，而因為函式在賦值運算子的右側，所以屬於表達式。由於它不一定要賦予名字，所以又被稱為*匿名函式*。需特別注意的是它的使用順序，因為它屬於值，所以並不會提升(Hoisting)，如果需要使用這個函式的話，應該將它列在前面的位置。

```javascript
a(1);    // error: a is not a function; 因為沒有hoisting

var a = function(x){
    console.log(x+x)
}
```

### 函式陳述式(Function Declarations)

函式陳述式的宣告方式就像是變數一樣，所以屬於陳述式，又因為它必須擁有一個名稱，因此又稱為*具名函式*。因為類似於變數，所以它會被提升，在使用上比較不需要擔心它擺放的位置。

```javascript
a(1);  // 2 因為hoisting的關係，並不會顯示undefined

function a(x){
    console.log(x+x)
}
```

## 資料來源

以上就是表達式和陳述式的差別，感謝以下資料來源
[1] [JavaScript 表達式觀念及運用 - JS Expression](https://wcc723.github.io/development/2020/09/17/js-expression/)
[2] [JS 原力覺醒 Day07 - 陳述式 表達式](https://ithelp.ithome.com.tw/articles/10218937)
[3] [JavaScript Expressions and Statements](https://medium.com/launch-school/javascript-expressions-and-statements-4d32ac9c0e74)
[4] [JavaScript 的赋值表达式和左表达式:Left-Hand-Side Expressions](https://zhuanlan.zhihu.com/p/25428739)
