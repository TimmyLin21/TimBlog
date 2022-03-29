---
title: 何謂Type coercion (強制轉型)？
date: 2022-03-29 10:51:56
tags: [JavaScript, type coercion]
categories:
- JavaScript
description: 在開始探討type coercion有多體貼 (邪惡) 之前，我想先解釋type coercion的定義。根據MDN的解釋，type conversion與type coercion都具有將型別轉換的意思，而最大的不同點在於type coercion是隱性的型別轉換，type conversion則可以包含顯性和隱性的型別轉換。
---
## 前言

在開始探討type coercion有多體貼 (~~邪惡~~) 之前，我想先解釋type coercion的定義。根據MDN的解釋，*type conversion*與*type coercion*都具有將型別轉換的意思，而最大的不同點在於*type coercion*是隱性的型別轉換，*type conversion*則可以包含顯性和隱性的型別轉換。[[1]](https://https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion) 但在這篇文章中，我想採用You Don't Know JS書中比較直觀的區分的方法，*implicit coercion* (隱性強制轉型) 和 *explicit coercion*（顯性強制轉型）。[[2]](https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/types%20%26%20grammar/ch4.md)

## 抽象的值運算

首先，我們需要先暸解抽象的值運算做了什麼事情，因為不論是顯性或者隱性強制轉型都會經過抽象的值運算，可以把它想像成JavaScript在轉換過程中會使用到的工具，而這些值運算包括了`ToString`、`ToNumber`、`ToBoolean`和`ToPrimitive`。

### ToString

任何非string值被轉型為string時，會依照ECMAScript2022的指南做轉換。基本上，比較常見的型態`Undefined`、`Null`和`Boolean`會直接轉成相對應值的字串，而`Number`常用的規則為

* NaN => "NaN"
* +0/-0 => "0"
* +∞ => "Infinity"
* x => "x"
* x代表數字
* -x => "-x"

![To string conversion table](https://i.imgur.com/GOuft81.png)

### ToNumber

任何非number值被轉型為number時，會依照ECMAScript2022的指南做轉換。

* String若不是數字則會變成NaN

![To number conversion table](https://i.imgur.com/vyZome4.png)

### ToBoolean

只要是*Truthy*（真值）就會轉換成true，相反地，只要是*Falsy*就會轉換成false。由於Truthy的數量太多，所以記住哪些是Falsy會比較容易。

#### Falsy

* Undefined
* Null
* +0,-0 or NaN
* 空字串

![To boolean conversion table](https://i.imgur.com/kzck2IU.png)

### ToPrimitive

主要用於將物件轉換為基本型別，其運作方式為先檢查該值是否有`valueOf`方法，如果有的話，則使用此方法將值轉換，若沒有，則使用`toString`的方法將值做轉換，假如兩種方法都沒有時，則回傳`TypeError`。

## Explicit Coercion 顯性強制轉型

瞭解完抽象的值運算後，終於可以進入強制轉型的部分了。顯性強制轉型指的是刻意使用方法或運算子改變值的型態，常見的型態轉換為 `String <-> Number` 、`Numeric String`和`all -> Boolean`。

### String <-> Number （顯性）

字串與數字間的顯性強制轉型，常見的方法有以下三種。

#### 方法一： 使用內建函式 `String(..)` 和 `Number(..)`

#### 方法二： 使用物件原型的方法 `.toString()`

`String(..)`是直接調用`.toString`來轉字串，而兩者的差異是`null`或者`undefined`使用`toString`會報錯而`String(..)`則不會。

```javascript
String(null); // "null"
String(undefined); // "undefined"
String(12345); // "12345"

null.toString(); // Uncaught TypeError: Cannot read property 'toString' of null
undefined.toString(); // Uncaught TypeError: Cannot read property 'toString' of undefined
12345.toString(); // Uncaught SyntaxError: Invalid or unexpected token
```

#### 方法三：使用一元正/負運算子`＋`、`－`

使用這個方法有個缺點，就是容易造成各種語意上的誤會，例如與遞增(++)和遞減(--)或者二元運算子的加(+)和減(-)混淆。

```javascript
+'123'  // 123
-'-123' // 123
```

### 數值字串(Numeric String)

當字串內容包括了文字與數字時，使用`Number(..)`轉型會得到NaN的結果，但是還有另一種方法`parseInt(..)`。這種方法會由左至右逐一將字串中的數字轉換為`number`，當遇到了非數字的字元時就會停止轉換。
除此之外，`parseInt(..)`的第二個參數可以用來指定轉換的基數，若沒有加入參數，則會以頭幾個字元決定基數為何，例如：開頭若是`0X`就會轉為十六進位的數字。所以如果想要以十進位做轉換，最好在第二個參數加入10。

```javascript
let a = '123px';

Number(a);      // NaN
parseInt(a,10); // 123
```

### 全部型別 -> Boolean （顯性）

#### 方法一： 使用內建函式 `Boolean(..)`

一樣是調用`ToBoolean`來進行轉換，所以規則相同。

* `Truthy` -> `true`
* `Falsy` -> `false`

```javascript
Boolean('Hello World'); // true
Boolean([]); // true
Boolean({}); // true
Boolean(null); // false
Boolean(undefined); // false
Boolean(NaN); // false
Boolean(0); // false
Boolean(''); // false
```

#### 方法二： 否定運算子`！`

雙重否定運算子也能夠將值強制轉換成布林值

```javascript
!!'Hello World'; // true
!![]; // true
!!{}; // true
!!null; // false
!!undefined; // false
!!NaN; // false
!!0; // false
!!''; // false
```

## Implicit Coercion 隱性強制轉換

終於到了最令人頭痛的隱性強制轉換了，由於其機制比較不合乎常理所以常常使人崩潰，但其實在搞懂背後運作的邏輯後，可以節省不少程式碼的長度。這裡舉出幾種比較常見的隱性強制轉換。

### String <-> Number (隱性)

#### `＋`運算子

若兩運算元的型別不同，其中一方為字串時，就會將另一方強制轉型為字串，而轉換的規則與`ToString`相同。

```javascript

const a = '1';
const b = 1;
const c = [1, 2];
const d = [3, 4];

a + 1; // "11"
b + 1; // 2
b + ''; // "1"
c + d; // "1,23,4"
```

#### 其他運算子

除了`＋`號以外的其他數學運算子，如`－`、`＊`和`／`，如果其中一方為非數字則會使用`ToNumber`強制轉型為數字。

```javascript
const a = '1';

a - 0; // 1
a * 1; // 1
2 / a; // 2
```

### 全部型別 -> Boolean (隱性)

#### 條件判斷式

像是常見的`if`、`while`和三元運算式中的條件判斷式，都會強制轉型成Boolean。

```javascript
let a = 12345;
let b = 'Hello World';
let c; // undefined
let d = null;

if (a) {
  // true
  console.log('a 是真的'); // a 是真的
}

while (c) {
  // false
  console.log('從來沒跑過');
}

c = d ? a : b;
c; // "Hello World"
```

#### 邏輯運算子`||`和`&&`

邏輯運算子的運作原理是將第一個運算元強制轉型為Boolean

* 在`||`(or)的狀況下，若結果為true，則取第一個運算元作為結果; 若結果為false，則取第二個運算元為結果。
* 在`&&`(and)的狀況下，若結果為true，則取第二個運算元作為結果; 若結果為false，則取第一個運算元為結果。

```javascript
const a = 12345;
const b = 'Hello World';
const c = null;

a && c;         // null
a && b;         // 'Hello World'
undefined && b; // undefined

a || b;         // 'Hello World'
c || a;         // 12345
```

### 寬鬆相等 `==` (Loose Equals)

當型別不同時，會先將其中一個或兩個值做強制轉型再做比較。其規則如下：

* `String` -> `Number`
* `Boolean` -> `Number`
* `Null`和`undefined`會轉型為彼此，因此是相等的
* 物件使用`toPrimitive`轉換為基本型別

更多的資訊可以參考[對照表](https://dorey.github.io/JavaScript-Equality-Table/)

## 資料來源

以上就是對強制轉型的介紹，在此感謝以下資料來源
[1] [Type coercion - MDN](https://https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion)
[2] [You Don't Know JS: Types & Grammar](https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/types%20%26%20grammar/ch4.md)
[3] [你懂 JavaScript 嗎？#8 強制轉型（Coercion）](https://cythilya.github.io/2018/10/15/coercion/#%E6%8A%BD%E8%B1%A1%E7%9A%84%E9%97%9C%E4%BF%82%E5%BC%8F%E6%AF%94%E8%BC%83)
[4] [ECMAScript 2022](https://tc39.es/ecma262/#sec-tonumber)
[5] [瞭解JavaScript中的型別轉換](https://iter01.com/631635.html)
