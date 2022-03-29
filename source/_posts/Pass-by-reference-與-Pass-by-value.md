---
title: Pass by reference 與 Pass by value
date: 2022-03-29 10:37:30
tags: [JavaScript, pass by reference, pass by value]
categories:
- JavaScript
description: JavaScript的資料可以分成基本型別(Primitives) 和物件型別(Object)。其中基本型別指的是以純值型式存在的String、Number、Boolean、Null和Undefined等，而物件型別指的則是可能由零或多種不同型別（包括純值與物件）所組成的物件。
---
## 前言

JavaScript的資料可以分成**基本型別(Primitives)** 和**物件型別(Object)**。其中基本型別指的是以純值型式存在的`String`、`Number`、`Boolean`、`Null`和`Undefined`等，而物件型別指的則是可能由零或多種不同型別（包括純值與物件）所組成的物件。

## Pass by value (傳值)

基本型別使用的傳遞方式是pass by value，意思是說假設有兩個變數a和b，當b賦予a值的時候，會在記憶體產生一個新的空間並複製b的值到新的空間中，所以a和b所參照的記憶體空間是不同的。

```javascript
let a=1;
let b=2;
a=b;
a+=1;
console.log(a,b); // 3 2
// b並不會應該a的值改變而跟著變動，因為兩者參照的記憶體空間不同
```

## Pass by reference(傳址)

物件型別使用的傳遞方式則是pass by reference，在傳遞時並不會產生一個新的記憶體空間，取而代之的是物件會參考相同的記憶體空間，所以當其中一個變數做更動時，另一個變數也會被影響。

```javascript
let a={
    name:'Tim',
    sex:'male'
}
let b={
    name:'Carol',
    sex:'female'
}
a=b
a.name='Triana'
console.log(a,b);
// a={name:'Triana',sex:'female'}
// b={name:'Triana',sex:'female'}
```

![記憶體空間改變](https://i.imgur.com/8hNoIDD.png)

## Pass by sharing

物件型別在傳遞時有一個特例，就是如果將物件當成參數帶入function時，如果物件被重新賦予值的時候，外部的變數內容是不會被影響的，而這樣的傳遞方式則被稱為pass by sharing。

```javascript
let coin1={value:10};

function changeValue(obj){
  obj = {value:123};
}

changeValue(coin1);
console.log(coin1);  // 此時coin1仍然是 {value:10}
```

## 參考資料

以上就是pass by value和pass by reference的差異，在此感謝以下資料來源。
[1] [JavaScript 是「傳值」或「傳址」](https://kuro.tw/posts/2017/12/08/JavaScript-%E6%98%AF%E3%80%8C%E5%82%B3%E5%80%BC%E3%80%8D%E6%88%96%E3%80%8C%E5%82%B3%E5%9D%80%E3%80%8D/)
[2] [[JavaScript] Javascript中的傳值 by value 與傳址 by reference](https://medium.com/itsems-frontend/javascript-pass-by-value-reference-sharing-5d6095ae030b)
[3] [Pass-By Value vs. Pass-By Reference in JavaScript](https://levelup.gitconnected.com/pass-by-value-vs-pass-by-reference-in-javascript-82fa8736c9f7)
