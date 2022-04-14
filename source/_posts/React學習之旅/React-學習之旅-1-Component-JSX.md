---
title: 'React 學習之旅(1) - Component, JSX, Props'
date: 2022-04-11 17:21:55
tags: [React, Component, JSX, Props]
categories:
- React 學習之旅
description: 有鑒於工作與求職上都會面對 React 的需求以及秉持著小孩才做選擇，我全都要的精神，在我對 Vue 有了一定暸解之後，我決定繼續踏上學習 React 的旅程。而我這次學習的管道選擇的是在 Udemy 的 React - The Complete Guide (incl Hooks, React Router, Redux) 所以如果有上過一樣的課程，應該會蠻熟悉未來會提到的觀念，也可以把它當複習來看。由於這一系列的文章會比較類似學習心得，所以在內容上可能會有許多概念混合在一篇文章之中，我盡量會把關鍵字列在標題上以方便後續做查詢，就讓我們開始學習 React 吧！
---
## 前言

有鑒於工作與求職上都會面對 React 的需求以及秉持著 `小孩才做選擇，我全都要` 的精神，在我對 Vue 有了一定暸解之後，我決定繼續踏上學習 React 的旅程。而我這次學習的管道選擇的是在 Udemy 的 React - The Complete Guide (incl Hooks, React Router, Redux) ~~非工商~~，所以如果有上過一樣的課程，應該會蠻熟悉未來會提到的觀念，也可以把它當複習來看。由於這一系列的文章會比較類似學習心得，所以在內容上可能會有許多概念混合在一篇文章之中，我盡量會把關鍵字列在標題上以方便後續做查詢，就讓我們開始學習 React 吧！

![Vue 和 React 我全都要](https://i.imgur.com/A1gDgur.png)

## React vs JavaSript

在進入如何撰寫 React 之前，先讓我們來聊聊為什麼要學習 React 吧！以 Todolist 為例，在以往如果你使用純 JS 去撰寫，往往會花很多的時間在 DOM 節點的取得、新增以及修改上。但若是使用 React 去進行撰寫，React 會為你處理有關 DOM 的部分，而你只要專注在邏輯的部份就好，這樣大幅提升了開發者的開發效率。除此之外，React 能夠輕易地建立 Single Page Application (SPA)，而什麼是 SPA 呢？最常見的使用案例大概就是大家習以為常的 Gmail，在每次點擊畫面時，並不會像伺服器請求頁面，而是針對特定的部分或是整個頁面進行渲染，這樣的好處是等待時間較短且畫面並不會有跳轉的感覺，能夠大幅增加使用者的體驗。而不論是 React, Vue 或者 Angular，都有類似的特性，因此這也就是為什麼會越來越多人選擇使用前端框架當作開發工具。

## React 專案的建立與架構

建立 React 專案前請記得安裝 Node.js，而安裝完成後只需要在終端機輸入 `npx create-react-app project-name`，就能成功建立一個 React 專案。在專案裡面，我們先來瞭接一些重要的檔案，首先 public 這個資料夾是不會經過 React 編譯的，而為什麼會需要編譯呢？在等等會多做介紹，這裡只要先知道這個事實就好。所以一些不用編譯的檔案，例如 index.html 就會放在這個資料夾中。而另一個資料夾 src 則放入需要進行編譯的檔案。

```markdown
<!-- 目錄結構 -->
project  
│
└─public
│  │ index.html  
│
└─src
│  │ App.js
│  │ App.css 
│  │ index.js
│  │ index.css 
│
```

其中一個重要的檔案是 index.js，它就類似於 webpack 的進入點，所有需要進行編譯的檔案都應該與 index.js 有所關聯。這裡可以注意到我們引入了 ReactDOM 這個套件，這主要是用來建立一個虛擬的 DOM(Virtual DOM)，然後我們利用內建的函示 createRoot 將這個 Virtual DOM 綁定到 index.html 中 id 為 root 的真實 DOM 節點上並使用 render() 渲染出我們想要的畫面，也就是待會會提到的第二個重要檔案 App。其實 React 專案中只會有一個 html 檔案，也就是 index.html，因此所有畫面的變動都是操控同一個頁面，這也就是為什麼會被稱為 SPA。

```js
// index.js
import ReactDOM from 'react-dom/client';

import './index.css';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

App.js 是 React 專案中的 root component，而什麼是 component 呢？我會在等等對 component 做一個完整的介紹，這裡只要先知道如同 index.js 的概念，所有的 component 也都需要與 App.js 有所關聯就好了。

## Components

`React is all about components`，這句話是在介紹 React 時，常聽到的解釋，也就可以知道 component 對於 React 的重要性，那什麼是 components 呢？ 我們可以把它跟理想的函式概念做聯想，我們會希望一個函式最好是可以重複使用，而且容易維護，不會與其他函示做太多的耦合（coupling)，而 component 也使用相同的概念，將畫面拆分成一個個可以重複使用且低耦合的小單位，這樣我們就可以省略許多重複的程式碼也比較好進行後續的管理。而一個 component 其實是一個 JavaScript 的函示，回傳我們想要呈現的結構，還能引入 css 進行樣式變更。具體如下：

```js
import './Component.css';

function Component() {
  return (
    <div>
      <h1>Hello world!</h1>
    </div>
  );
}

export default Component;
```

而當你建立好一個 component 之後你可以輸出(export)並引入(import)到其他 component 之中，下面的範例是將剛剛創建好的 component 引入到 app.js 這個 root component 中，使用方法就像是自定義一個 HTML 標籤一樣，可以無限制的增加數量。

```js
import Component from "./Component";

function App() {
  return (
    <div>
      <h2>Let's get started!</h2>
      <Component />
      <Component />
      <Component />
    </div>
  );
}

export default App;
```

## JSX

而初次使用 React 可能會很訝異，為什麼函示可以回傳 HTML 的結構呢？ 這是因為它其實是 React 所使用的特殊語法 JSX，可以把它想成是 JavaScript 與 HTML 的合體。而在使用 JSX 時，也幾點是與 HTML 不太一樣的，第一點是 class 需要改成 className，這是因為 class 在 JS 中屬於保留字，故需要另外取一個名字做替換。

```js
function component() {
  return (
    <div className="title">
      <h1>Hello world!</h1>
    </div>
  );
}

export default component;
```

第二個點是可以使用 {} 來處理動態資料，{} 內可以放入任何表達式，例如：

```js
function component() {
  const firstName = 'Tim'
  const secondName = 'Lin'
  return (
    <div className="title">
      <h1>Hello { firstName + secondName }!</h1>
    </div>
  );
}

export default component;
```

最後 component 只允許一個根節點，下面分別有一個錯誤以及正確示範。

```js
// 錯誤
function component() {
  return (
    <div />
    <div />
    <div />
  );
}

export default component;

// 正確
function component() {
  return (
    <div>
      <h1>Hello</h1>
    </div>
  );
}

export default component;
```

## Props

前面我們有提到動態資料是用 {} 來呈現，但現在問題來了，我們要如何從其他 component 去取得資料呢？如果是從父層取得資料，我們可以使用 Props，至於其他傳遞方式，在未來的篇幅中，會再為大家做介紹。讓我們回到 Props，首先，我們在父層的函示內定義常數 name，接著在子元件的標籤中給予 `name = { name }`的屬性，第一個 name 代表的是等等取用值會使用的 key，而第二個 name 則是我們要傳入的值，也就是 `'Tim'`。之後，值在內層的元件中會以參數的型式傳入，通常會把它取名為 props，而我們就可以搭配剛剛的 key 值取得我們所需要的 name 了。

```js
import Component from "./Component";

function App() {
  const name = 'Tim'
  return (
    <div>
      <h2>Let's get started!</h2>
      <Component name = { name } />
    </div>
  );
}

export default App;
```

```js
function component(props) {
  return (
    <div>
      <h1>Hello { props.name }!</h1>
    </div>
  );
}

export default component;
```

## UI components

而有時我們會遇到某些 component 有共用的樣式，例如： card, modal 或者 alert 等等。這時我們就可以選擇將共用的部分取出來製作成 UI components，這樣就能夠讓程式碼更為精簡而且也避免重複生成類似的元件。在以下的例子中，會以一個 card 的 UI component 做介紹。因為 card 內部會有其他結構，因此我們會以 props 的方式傳入，
在 className 的部分，我們需要將標籤上的 className 也加入到內部的 className 中，因此需要用字串組合的方式動態生成新的 className，而 `props.children` 則是用於保留在父層中 Card 元件內部的結構，也就是 h2 和 Component。這樣，以後想要製作出相同風格的 Card，只要在最外層加上 Card 的 tag，就可以快速的完成了喔！

```js
// UI component - card
import './Card.css';

function Card(props) {
  const classes = `card ${props.className}`;
  return (
    <div className={ classes }>{ props.children }</div>
  )
}

export default Card;
```

```js
import Component from "./Component";
import Card from "../UI/Card";

function App() {
  const name = 'Tim'
  return (
    <Card className="container">
      <h2>Let's get started!</h2>
      <Component name = { name } />
    </Card>
  )
}

export default App;
```

## 結語

以上就是 React 學習之旅的第一章，在這裡我們暸解到了 Component, JSX 和 Props。之後會為大家分享更多學習心得，那就下次見囉！
