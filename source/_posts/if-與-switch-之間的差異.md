---
title: if 與 switch 之間的差異
date: 2022-03-29 10:46:14
tags: [JavaScript, if, switch]
categories:
- JavaScript
description: 基本上，if/else 與 switch在大部分的情況下可以互相替換使用，但兩者還是有些許不同，但在探討兩者之間的差異之前，我們可以先瞭解兩者的基本架構。
---
基本上，if/else 與 switch在大部分的情況下可以互相替換使用，但兩者還是有些許不同，但在探討兩者之間的差異之前，我們可以先瞭解兩者的基本架構。

## 基本架構

### if/else

```javascript
if(條件式){
  // 如果符合第一個條件式，執行此區塊內程式  
}else if(條件式){
  // 如果符合第二個條件式，執行此區塊內程式
}else{
  // 如果上述條件式都不符合，則執行此區塊內程式
}
```

if/else的規則為假使條件式為true，則執行後方區塊內的程式，但假如是false，則依序接著下一個條件式做判斷。途中，假使有一個條件式為true，就不會執行後續的條件式判斷，但是如果每個條件式都是false的話，則會執行else區塊內的程式。
其中有一些小細節可以注意：

* else if和else並不一定要使用，可以只建立if的條件式判斷。
* else if沒有數量上的限制。
* 每個執行區塊中還能夠包覆另一個if/else，但須注意避免過度巢狀。

```javascript
if(條件式){
  if(條件式){
    if(條件式){
        
    }
  }
}
// 過度巢狀會變得難以閱讀程式碼
```

### switch

```javascript
switch(參數){
  case 值:
  // 如果值與表達式相符，執行下方程式
  break;
  // 終止程式
  
  case 值:
  break;
  
  default:
  // 如果上述值都不相符，則執行下方程式
}
```

switch的規則為假如參數符合case後方的值則執行`：`後方的程式，但必需使用break終止程式，否則會執行到最後。而假設所有的值都不符合的話，就會執行default後方的程式。

##  效能差異

在瞭解完兩者的基本架構後，可能會有些人疑惑那到底哪一個比較快呢？直接說結果的話，switch的速度會稍微快一些，但除非是大型專案中需要判斷的內容非常多，不然兩者之間的差異並不明顯。
其背後的原理，簡單來說，switch在編譯時會生成`jump table`，使得其在執行階段時相較於if/else是一層層判斷哪一個條件式是符合的，switch則是決定哪一個case是需要被執行的。

## 總結

總結來說，兩者在選擇上的考慮點主要是可讀性和邏輯的判斷，if/else由於可以不斷得巢狀所以並不會像switch一樣簡單易讀。而在邏輯的部份，對我來說，switch比較適合運用在值簡單只需要比對是否相同的狀況，而if/else則可以配合邏輯運算子做較為複雜的判斷。

## 資料來源

以上就是if/else和switch之間的差別，在此感謝以下資料來源。
[1] [重新認識 JavaScript: Day 09 流程判斷與迴圈](https://ithelp.ithome.com.tw/articles/10191453)
[2] [JavaScript: Switch vs. If Else](https://medium.com/@michellekwong2/switch-vs-if-else-1d458e7b0711)
[3] [JavaScript 的 if 跟 switch 效能](https://hsiangfeng.github.io/javascript/20200117/3217748743/)
[4] [What is a jump table?](https://stackoverflow.com/questions/48017/what-is-a-jump-table)
