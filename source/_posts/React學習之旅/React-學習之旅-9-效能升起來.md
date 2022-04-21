---
title: React 學習之旅(9) - 效能升起來
date: 2022-04-21 10:46:37
tags: [React, ReactDOM, memo, useCallback, useMemo]
categories:
- React 學習之旅
description: 在這一篇文章中，想跟大家聊聊 React 和 ReactDOM 的作用以及背後運行的邏輯，藉由這些觀念我們就可以使用一些小技巧去提升網頁的效能，除此之外，也會提到 state 的更新方法與機制，就讓我們一起來暸解吧！
---
## 前言

在這一篇文章中，想跟大家聊聊 React 和 ReactDOM 的作用以及背後運行的邏輯，藉由這些觀念我們就可以使用一些小技巧去提升網頁的效能，除此之外，也會提到 state 的更新方法與機制，就讓我們一起來暸解吧！

## React 和 ReactDOM

在 React App 中，我們建立了許多的 function components，而這些元件最終的目的就是回傳 JSX。透過這些 JSX，React 就能知道這些元件應該如何呈現。而在元件中，我們還能使用 props， useState 或者 useContext 等功能去變更元件內的資料，每當元件的資料變更時，React 就會重新評估，也就是重新執行 function components。而執行過後元件可能獲得新的或者一樣的 DOM 結構，這時 ReactDOM 就會去做比對，如果比對的結果不一樣，ReactDOM 就會針對不一樣的地方操控 real DOM 進行變更，由以上的機制我們可以知道重新評估並不等於重新渲染。

![React 和 ReactDOM 的功用](https://i.imgur.com/CGKEyrg.png)

## React.memo()

每次資料變更時元件都會重新執行一次，但是元件中資料沒有更新的地方也會重新執行，這對於一些大型的元件在效能處理上就會比較差，因此我們就可以使用 React.memo() 去告訴 React 哪些部分如果資料沒有更新的話是不需要重新執行的。如下方這個元件，在輸出的部分使用 React.memo() 傳入 DemoOutput，這樣只有在 DemoOutput 的 props 進行變更時，這個元件才會被重新執行，而內層的 MyParagraph 元件也一樣不會重複執行。

```js
import React from 'react';

import MyParagraph from './MyParagraph';

const DemoOutput = (props) => {
  console.log('DemoOutput RUNNING');
  return <MyParagraph>{props.show ? 'This is new!' : ''}</MyParagraph>;
};

export default React.memo(DemoOutput);
```

你可能會想說要是這樣能夠增加效能，那為什麼不把全部的元件都改成 React.memo() 呢？這是因為 React.memo() 還是需要使用記憶體去儲存之前的狀態，除此之外在每次更新時也需要進行比對，這些都會消耗到效能。對於小型的元件來說可能 React.memo() 所使用的效能可能會操過原本的方法，因此在使用前還是必須要評估說這個元件是否會頻繁的更新，如果是可能就不那麼適合使用這個方法。

## useCallback

在使用 UI components 時，我們傳入的 props 通常是一個函式，而這個函式也經常是固定的，如同下方的 toggleParagraphHandler。若我們使用 React.memo() 套用在 Button 這個元件上，當資料變更時，Button 應該是不會重新執行的，但是實際執行後就會發現 Button 被重新執行了。會出現這個現象的原因是因為每次在重新執行 App() 時，都會建立一個新的 toggleParagraphHandler，而我們知道函式也是一個物件，物件具有傳參考的特性，就算他們長得一模一樣，也是不相等的。因此對於 Button 來說，每次在重新執行時，傳入的都會是新的函式，而為了解決這個問題，就可以使用 useCallback()。

```js
import Button from './components/UI/Button/Button';
import DemoOutput from './components/Demo/DemoOutput';
import './App.css';

function App() {
  const [showParagraph, setShowParagraph] = useState(false);

  console.log('APP RUNNING');

  const toggleParagraphHandler = () => {
    setShowParagraph((prevShowParagraph) => !prevShowParagraph);
  };
 
  return (
    <div className="app">
      <h1>Hi there!</h1>
      <DemoOutput show={showParagraph} />
      <Button onClick={toggleParagraphHandler}>Toggle Paragraph!</Button>
    </div>
  );
}

export default App;
```

useCallback 可以將函式儲存起來，每次重新執行時，就會將舊的函式賦予到新的函式上，除此之外就像是 useEffect 一樣需要給定 dependencies，也就是當某些值變更時，函式就會進行更新。使用的方式只需要將原本的函式用 useCallback() 包覆住即可，這樣就不會再重新執行 Button 了。

```js
import React, { useCallback, useState } from 'react';

import Button from './components/UI/Button/Button';
import DemoOutput from './components/Demo/DemoOutput';
import './App.css';

function App() {
  const [showParagraph, setShowParagraph] = useState(false);

  console.log('APP RUNNING');

  const toggleParagraphHandler = useCallback(() => {
    setShowParagraph((prevShowParagraph) => !prevShowParagraph);
  },[]);
 
  return (
    <div className="app">
      <h1>Hi there!</h1>
      <DemoOutput show={showParagraph} />
      <Button onClick={toggleParagraphHandler}>Toggle Paragraph!</Button>
    </div>
  );
}

export default App;
```

## useMemo

除了函式之外，如果今天想要儲存的值是一個不變的陣列或者物件時，就可以使用 useMemo。像是下方的例子中，會從外部傳進一組陣列，之後再利用 sort() 排序成一個新的陣列，但我們只想要在傳進來的陣列不一樣時才重新執行，這時我們就可以使用 useMemo() 把計算過的陣列儲存起來。而使用的方法就跟 useCallback() 類似需要加上 dependencies，但 useMemo 必須回傳一個值而 useCallback() 則不需要。對於這種比較消耗效能的陣列方法就非常適合使用 useMemo 將計算結果儲存起來降低效能的損耗。

```js
import React, { useMemo } from 'react';

import classes from './DemoList.module.css';

const DemoList = (props) => {
  const { items } = props;

  const sortedList = useMemo(() => {
    console.log('Items sorted');
    return items.sort((a, b) => a - b);
  }, [items]); 
  console.log('DemoList RUNNING');

  return (
    <div className={classes.list}>
      <h2>{props.title}</h2>
      <ul>
        {sortedList.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
};

export default React.memo(DemoList);
```

## State 更新與排程

通常我們在使用 useState 進行資料管理時，我們會在元件的函式內加上 `const [showParagraph, setShowParagraph] = useState(true)` 像這樣的程式碼，但如同上面所說的，假設元件重新執行一次的話，那每一次執行到這個段落時都應該會把 showParagraph 設定成預設值 true。但其實 React 會在第一次執行後記住這個 state 是屬於哪一個元件的，之後每當這個元件重複執行時，他就不會重新賦予預設值而是看需求更新 state 的值。除非元件被移除再新增才有可能將新的 state 到賦予預設值。

接著我們來看看 state 是如何更新的。假設今天有一個元件叫做 Component，我們使用 state 進行資料管理並給定一個預設值 true。當我們使用 setShowParagraph(false) 想要將 state 改為 false 時並不會馬上就執行，而是會進入 React 的排隊順序中。在 React 的排隊順序中，每個元件有不一樣的優先順序，但可以肯定的是相同的元件會依造給定的請求順序進行更新，由於更新的先後順序並不一定，所以在之前的文章中才會建議使用函式的形式去取用 prevState 以確保取得的值是最新的。等到 state 更新完畢後，就會再次的執行 Component，這就是 state 的更新流程。

![state 的更新流程](https://i.imgur.com/eN5N1aK.png)

## 結語

以上就是有關 React 和 ReactDOM 機制以及一些可以達到效能優化方法的介紹，對於要不要使用 memo 或者 callback 可能還是需要足夠的經驗才能判定哪一種方式對於效能的表現會是最好的，在這裡就給大家做一個參考而已。那我們就下次見囉！
