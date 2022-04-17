---
title: React 學習之旅(5) - Fragments，portals 和 refs
date: 2022-04-15 19:16:54
tags: [React, Fragments, portals, refs]
categories:
- React 學習之旅
description: 相信大家學習到這裡可能有發現 React 在編譯 jsx 時有一些限制，例如：根目錄只能有一個或者像是 Components 被包覆在層層的 div 當中等等，而今天就要為大家介紹一些實用的工具去處理這些問題，就讓我們一起來瞭解 fragments，portals 和 refs 吧。
---
## 前言

相信大家學習到這裡可能有發現 React 在編譯 jsx 時有一些限制，例如：根目錄只能有一個或者像是 Components 被包覆在層層的 div 當中等等，而今天就要為大家介紹一些實用的工具去處理這些問題，就讓我們一起來瞭解 fragments，portals 和 refs 吧。

## Fragments

在建立 Components 時，如果同時擁有兩個以上的根目錄，就會跳出 `Adjacent JSX elements must be wrapped in an enclosing tag.` 的錯誤，這是因為回傳的值其實是 `React.createElement('html tag', {}, 'Content')`，若有兩個根目錄，就表示要回傳的是兩個值，而這是在 JavaScript 中不被允許的，所以才會出現錯誤訊息。

而這時你可能會想說，那我就在最外層加一個 div 不就好了嗎。的確，加上 div 就不會發生錯誤，但是這可能會導致樣式套用上出現錯誤，或者以 Sementic HTML 的角度來看，就會有很多沒有意義的包覆而這也會使得瀏覽器的渲染速度降低。這時我們就可以使用到我們之前學習的 `props.children`。

我們先建立一個 Wrapper.js，建立的形式類似於 Components 但是我們不包覆任何 HTML 的結構，只回傳 props.children，之後再用 Wrapper 以 Components 的形式作為根目錄，這樣就能回傳 Wrapper 內的內容，而且因為回傳的只有一個值也就不會出現錯誤訊息。

```js
// Wrapper.js
const Wrapper = (props) => {
  return props.children
};

export default Wrapper;
```

```js
import React from "react";
import Wrapper from "../Helpers/Wrapper";

const Modal = () => {
  return (
    <Wrapper>
      <header>
        <h2>Invalid input</h2>
      </header>
      <div>
        <p>content</p>
      </div>
      <footer>
        <button type="button">okay</button>
      </footer>
    </Wrapper>
  )
}

export default Modal;
```

不對啊，說了這麼多，那 Fragments 去哪了？其實 React 很貼心的提供了 Fragment 這個功能去完成剛剛 Wrapper 做到的事情，而使用的方法也非常的簡單，只要將最外層的 Wrapper 改成 React.Fragment 就好了，或者從 react 中引用 { Fragment } 這個方法，這樣只需要輸入 Fragment 就可以了。

```js
import React, { Fragment } from "react";
import Wrapper from "../Helpers/Wrapper";

const Modal = () => {
  return (
    <Fragment>
      <header>
        <h2>Invalid input</h2>
      </header>
      <div>
        <p>content</p>
      </div>
      <footer>
        <button type="button">okay</button>
      </footer>
    </Fragment>
  )
}

export default Modal;
```

## Portals

第二個要介紹的工具是 Portals，使用的情境是當有些佔據整個螢幕空間的 Components，如: Modal，被包覆在比較深層的結構中，這時對於一般使用者來說，視覺效果並沒有太大的變化，但是對於 Screen Reader 來說，就會顯得有點突兀。而 Portals 就能幫我們將 Components 傳送到我們想要的 DOM 位置。

在使用上，我們需要先在實體 DOM 定義我們想要傳送的位置，舉例來說，在 body 內建立一個 id 名稱為 modal-root 的 div，而這就是我們等等要傳送到的位置。

```html
<!-- index.html -->
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="modal-root"></div>
  <div id="root"></div>
</body>
```

接著需要引入 ReactDOM，引入的方法為 `import ReactDOM from 'react-dom';`，若是使用 React18 則是 `import ReactDOM from 'react-dom/client';`。引入後我們就可以使用裡面的函式 createPortal()，這個函式會有兩個參數，第一個是 component，需要以 JSX 的形式輸入，而第二個則是指定的實體 DOM 位置，撰寫的方式則是用原生 JavaScript 取得節點的方式，也就是 `document.getElementById('')`，這樣就能將 Modal 傳入我們想要的位置了。

```js
import React, {useState} from 'react';
import ReactDOM from 'react-dom';

import Modal from '../UI/Modal';

function Component() {
  const [content, setContent] = useState('');
  const [isError, setIsError] = useState(false);

  const cancelHandler = () => {
    setIsError(false);
  };

  return (
    <React.Fragment>
      { ReactDOM.createPortal(<Modal content={content} onCancelError={cancelHandler} />, document.getElementById('modal-root'))}
    </React.Fragment>
  );
}

export default Component;
```

## Refs

最後一個功能是 Refs，他可以用來取得目前渲染畫面上 DOM 的節點，使用方法一樣需要先從 React 引入函式，這次引入的是 { useRef }，在引入之後會定義一個參數用來賦予 useRef() 回傳的值。這個值是一個物件，包含了一個 current 的屬性，而值就是我們想要的節點。而最後我們只需要將要取得的節點賦予對應的 ref 屬性就好。

```js
import React, {useRef} from 'react';


function Component() {
  const nameInputRef = useRef();
  const ageInputRef = useRef();
  return (
    <form>
      <label>UserName</label>
      <input type="text" ref={nameInputRef}/>
      <label>Age(Years)</label>
      <input type="number" ref={ageInputRef}/>
    </form>
  );
}

export default Component;
```

使用 Refs 我們可以更改畫面上的值而不需要重新渲染，相對於使用 State 進行資料管理，又被稱為 Uncontrolled components，因為這樣的操作方式是不經由 React 處理的，而是直接對真實的 DOM 進行修改，而使用 State 進行資料管理的 Components 則被稱為 Controlled components。雖然使用 Refs 比較簡單也可以讓程式碼比較精簡，但還是有一些限制，例如即時的表單驗證就無法完成，而更多的限制可以參考[這篇文章](https://goshacmd.com/controlled-vs-uncontrolled-inputs-react/)。

## 結語

以上就是一些在使用 React 時可以使用的實用工具，相信在實作上能夠幫助大家建構出更語意化的 HTML 結構，那我們就下次見囉！
