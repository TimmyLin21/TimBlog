---
title: 'var, let, const 的差別'
date: 2022-03-29 10:26:18
tags: [JavaScript, var, let, const]
categories:
- JavaScript
description: 在JavaScript中，宣告變數的方法有三種，分別是var，let還有const。而在ES6之前，只能使用var作為宣告的方法，在ES6之後，宣告分成了「變數」與「常數」，也就是let和const。
---
## 版本差異

在JavaScript中，宣告變數的方法有三種，分別是var，let還有const。而在ES6之前，只能使用var作為宣告的方法，在ES6之後，宣告分成了「變數」與「常數」，也就是let和const。
![var, let, const 差別圖](https://i.imgur.com/VHXG3Q9.png)
而ES5與ES6宣告之間的最大差別在於

1. **變數有效範圍(Scope)**
2. **提升(Hoisting)**
3. 重複宣告

## 變數有效範圍(Scope)

### 全域變數

var宣告的變數有效範圍的最小單位是以function做為分界的，而且容易變更到全域物件(window)的屬性，因此時常被稱為全域變數。而這樣的特性也造成了許多意外的bug，例如經典的for loop

```javascript
for (var i=0; i<10; i++){
    console.log(i);
    setTimeout(function(){
    console.log(`這執行第${i}次`)
    },10);
}
// result: 這執行第１０次 (10)
```

因為var會將i宣告為全域變數，透過for loop不斷累加到10才執行後面的setTimeout，因此才會得到１０次“這執行第１０次的結果”。

### 區域變數

而let和const的有效範圍是透過大括號{ }來切分的，因此又被稱為區塊作用域或是塊級作用域。

```javascript
{
    let a=1;
}
console.log(a); //跳出錯誤 a is not defined
```

除此之外，兩者所宣告的變數不會污染到全域物件(window)的屬性，所以被稱為區域變數。

```javascript
let a=1;
const b=1;

console.log(window.a,window.b);
// undefined undefined
```

## 提升(Hoisting)

除了有效範圍的不同之外，let和const也和傳統上var的提升方法有所差異。以前，使用var宣告變數時，會先創造出變數以及函式，之後再賦予其值。而在被賦予值之前，可以先呼叫變數。但是，新的宣告方法，let和const，在被賦予值之前會產生一個**暫時性死區(Temporal Dead Zone,TDZ)** ，這意味著我們無法在這過程中呼叫變數，必須等其值被賦予了，才能呼叫。

```javascript
console.log(a);  
var a=1;
// undefined

console.log(b);
let b=1;
// 跳出錯誤 Cannot access 'b' before initialization

console.log(c);
const c=1;
// 跳出錯誤 Cannot access 'c' before initialization
```

## 重複宣告

最後一點不同的是，let和const是不能夠使用相同的變數名稱重複宣告變數的，這一個特點大幅提升了製作大型專案時的方便性，因為當程式碼一多時，很常不小心宣告了相同的變數名稱，這時就會跳出錯誤的提示，避免不小心覆蓋掉之前所宣告的變數。

```javascript
let a=1;
let a=2; // 跳出錯誤 'a' has already been declared
```

## let 與 const

### let

let 除了上述的差異之外，其用法相似於var，如果這個變數的值會很常變更的話，就可以使用let做宣告。

```javascript
let cloudNum = 3; // 雲的數量不固定
```

### const

當變數的值為常數時，可以使用const做宣告，但如果使用const宣告變數，它的值就很難被重新賦予新的值。除此之外，在宣告時也不能像是var或者let不賦予其值。

```javascript
const sunNum=1;
sunNum=2;        //跳出錯誤 assignment to constant variable

const b;         //跳出錯誤 Missing initializer in const declaration
```

## 資料來源

以上就是var, let和const的差異，在此感謝以下資料來源
[1] [ES6 - let、const](https://ithelp.ithome.com.tw/articles/10242815)
[2] [搞懂變數作用域(2)- let 與const](https://ithelp.ithome.com.tw/articles/10199513)
[3] [鐵人賽：ES6 開始的新生活 let, const](https://wcc723.github.io/javascript/2017/12/20/javascript-es6-let-const/)
[4] [重新認識 JavaScript: Day 03 變數與資料型別](https://ithelp.ithome.com.tw/articles/10190873)
[5] [重新認識 JavaScript: Day 10 函式 Functions 的基本概念](https://ithelp.ithome.com.tw/articles/10191549)
