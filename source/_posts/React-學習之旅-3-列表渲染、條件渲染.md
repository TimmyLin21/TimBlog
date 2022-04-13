---
title: React 學習之旅(3) - 列表渲染、條件渲染
date: 2022-04-13 20:54:43
tags: [React, 列表渲染, 條件渲染, list rendering, conditional rendering]
categories:
- React 學習之旅
description: 今天要來介紹的是列表渲染以及條件渲染，之前我們已經初步的暸解到如何使用 JSX 撰寫我們想要呈現的元件到畫面上，但當資料一多時，不可能一筆一筆的重複列出我們想要的元件，這時就需要使用列表渲染的方式去解決。而除此之外，有時我們想要在點擊按鈕時呈現不見的畫面，這種情況就需要條件渲染。由以上的情境可以理解到這兩個概念是多麽的實用且重要，就讓我們一起來暸解吧！
---
## 前言

今天要來介紹的是列表渲染以及條件渲染，之前我們已經初步的暸解到如何使用 JSX 撰寫我們想要呈現的元件到畫面上，但當資料一多時，不可能一筆一筆的重複列出我們想要的元件，這時就需要使用列表渲染的方式去解決。而除此之外，有時我們想要在點擊按鈕時呈現不見的畫面，這種情況就需要條件渲染。由以上的情境可以理解到這兩個概念是多麽的實用且重要，就讓我們一起來暸解吧！

## 列表渲染

首先，我們必須了解在 {} 中如果放入一個陣列，且裡面的值是 JSX，那麼 React 就會逐一渲染陣列中的 JSX，例如： `{[<Card />,<Card />]}`，就會渲染出兩個相鄰的 Card 元件。而知道這個概念後，我們就能使用 map() 這個陣列方法將資料一筆筆轉化成我們想要的 JSX 結構。

```js
import React from "react";

const expenses = [
  {
    id: "e1",
    title: "Toilet Paper",
    amount: 94.12,
    date: new Date(2020, 7, 14),
  },
  { id: "e2", title: "New TV", amount: 799.49, date: new Date(2021, 2, 12) },
  {
    id: "e3",
    title: "Car Insurance",
    amount: 294.67,
    date: new Date(2021, 2, 28),
  },
  {
    id: "e4",
    title: "New Desk (Wooden)",
    amount: 450,
    date: new Date(2021, 5, 12),
  },
];

function Component() {
  const expenseList = expenses.map((expense) => (
    <li>
      <div>{expense.title}</div>
      <div>{expense.amount}</div>
      <div>{expense.date.toISOString()}</div>
    </li>
  ));
  console.log(expenseList)

  return (
    <ul>
      {expenseList}
    </ul>
  );
}

export default Component;
```

像這樣就不用重複建立四個 li，可以直接在畫面上呈現每一筆資料。但這時你可能會發現 console 出現一個錯誤的訊息，內容是你必須給予每一個項目一個獨一的 key 值。而這個 key 值在列表渲染中非常的重要，因為當你新增一筆資料後， React 渲染的方式並不是在新的節點上賦予新值，而是會比較新的陣列數量以及舊的節點數量，如果比較多，就新增節點，反之就減少節點，之後再將每個節點的資料進行更新。 Key 值的作用就在於告訴 React 每筆資料位於哪一個節點，有了 key 值，React 就不會在每次渲染時都需要比對每一個節點，只需要更新新的節點就好，這樣既能增加運行的效率也能避免出現不預期的 Bug。

```js
// 在最外層加上 id 屬性
<li id={expense.id}>
  <div>{expense.title}</div>
  <div>{expense.amount}</div>
  <div>{expense.date.toISOString()}</div>
</li>
```

## 條件渲染

React 條件渲染的方式有很多種，可以依照需求選擇適合的方法，以下會以當使用者點擊按鈕時，box 會依照 isShow 的值渲染或者消失當作不同種方法的範例。第一種是利用三元運算子進行判斷，當 isShow 的值為 true 就會渲染 box，false 的話就會是空字串。這種方法可以一行完成條件渲染，但當條件或者架構變得複雜時，JSX 的部分就會顯得比較雜亂，這時就可以使用第二種方法。

```js
import React, { useState } from "react";
import './Component.css';

function Component() {
  const [isShow, setIsShow] = useState(true);

  const clickHandler = () => {
    setIsShow(!isShow);
  }

  return (
    <div>
      {isShow?(<div className="box"></div>):''}
      <button onClick={clickHandler}>Toggle Box</button>
    </div>
  );
}

export default Component;
```

第二種方法是利用 && 或者 || 在 JS 的特性，當使用 && 時，若前者為 true，則回傳後者。而 ｜｜ 則是當前者為 false 時，會回傳後者。所以我們就可以把 JSX 改寫成以下的形式：

```js
import React, { useState } from "react";
import './Component.css';

function Component() {
  const [isShow, setIsShow] = useState(true);

  const clickHandler = () => {
    setIsShow(!isShow);
  }

  return (
    <div>
      {isShow && <div className="box"></div>}
      {isShow || ''}
      <button onClick={clickHandler}>Toggle Box</button>
    </div>
  );
}

export default Component;
```

若還是覺得 JSX 的部分看起來很雜亂，可以使用第三種方法，就是將判斷條件移到 JSX 外，我們先給定一個預設值，當條件符合時，則改變預設值。具體方法如下：

```js
import React, { useState } from "react";
import './Component.css';

function Component() {
  const [isShow, setIsShow] = useState(true);

  const clickHandler = () => {
    setIsShow(!isShow);
  }

  let content = <div className="box"></div>;
  if (!isShow) {
    content = '';
  }

  return (
    <div>
      {content}
      <button onClick={clickHandler}>Toggle Box</button>
    </div>
  );
}

export default Component;
```

若是想要把元件拆分的比較細，可以使用第四種方法，將之拆分為父子元件，子元件只用來進行條件渲染，而父元件則用來儲存值的變化，再透過 Props 的方式將值傳入到子元件中。具體方法如下：

```js
import React, { useState } from "react";
import ChildComponent from "./ChildComponent";
import './Component.css';

function Component() {
  const [isShow, setIsShow] = useState(true);

  const clickHandler = () => {
    setIsShow(!isShow);
  }


  return (
    <div>
      <ChildComponent state={isShow} />
      <button onClick={clickHandler}>Toggle Box</button>
    </div>
  );
}

export default Component;
```

```js
import React from "react";

function ChildComponent(props) {

  if (props.state) {
    return <div className="box"></div>;
  }

  return (
    ''
  );
}

export default ChildComponent;
```

## 結語

以上就是關於列表渲染以及條件渲染的介紹，相信在很多場合都會使用到這兩個技巧，那我們就下次見囉！
