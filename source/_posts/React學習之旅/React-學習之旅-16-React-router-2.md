---
title: React 學習之旅(16) - React router(2)
date: 2022-05-04 21:38:17
tags: [React, React router, NotFound, useHistory, Prompt, Query Parameters]
categories:
  - React 學習之旅
description: 上一篇文章中，已經為大家介紹 React router 的基本應用，而這次的分享主要是一些比較進階的使用方法，例如： NotFound、useHistory 和 Prompt 等等的使用方法，就讓我們一起來瞭解吧！
---
## 前言

上一篇文章中，已經為大家介紹 React router 的基本應用，而這次的分享主要是一些比較進階的使用方法，例如： NotFound、useHistory 和 Prompt 等等的使用方法，就讓我們一起來瞭解吧！

## NotFound

通常在一個網域中都會建立一個 404 的頁面，也就是當使用者輸入錯誤的網址時，告知使用者此頁面不存在的頁面。而錯誤路徑這麼多，我們該如何指定呢？很簡單，我們只要在路徑上使用 `*`，就能夠指定所有的網頁路徑，之後只要再搭配 Route 的運作原理，也就是只會顯示第一個符合的路徑，將包覆 NotFound 元件的 Route 放置到所有 Route 的最後面，這樣我們就能確保不會搶先其他元件渲染了。

```js
import { Route, Switch } from "react-router-dom";
import Welcome from "./pages/Welcome";
import Products from "./pages/Products";
import ProductDetail from "./pages/ProductDetail";

function App() {
  return (
    <Switch>
      <main>
        <Route path="/welcome">
          <Welcome />
        </Route>
        <Route path="/products" exact>
          <Products />
        </Route>
        <Route path='*'>
          <NotFound />
        </Route>
      </main>
    </Switch>
  );
}

export default App;
```

## useHistory

在前面的文章中，我們都是使用 Link 或者 NavLink 幫助我們進行網址的切換，但其實 React router 還提供了一個 custom hook 叫做 useHistory 來幫助我們更靈活地完成網址切換的動作。我們只需要將 useHistory 賦予到一個變數上，就可以使用它提供的方法，如：push 和 replace。push 和 replace 的差異在於會不會在瀏覽器留下紀錄，兩者都能夠進行轉址，但如果使用 replace 的話，就沒有辦法回到上一頁，而 push 則可以。

```js
import { useHistory } from 'react-router-dom';

const Component = () => {
  const history = useHistory();

  const clickHandler = () => {
    history.push('/');
  }

  return (
    <button onClick={clickHandler}>change url</button>
  )
}
```

## Prompt

當使用者在輸入表格時，如果不小心返回上一頁而需要重新填寫的話，想必他們心中只會有滿滿的恨意，而為了避免他們直接暴怒離開這個網站，React router 也提供了一個元件叫做 Prompt，來提醒使用者確定是否要不要離開網頁。

我們會將 Prompt 與其他結構並排，因此可以使用 Fragment 或者簡寫 <> 包覆，而在使用 Prompt 時，我們還需要輸入兩個 props，第一個是 when，用來告訴 Prompt 該在什麼時機觸發，只要是在 true 的狀況下離開頁面，就會觸發。在下方的例子中，我們用 isEntering 來判定使用者是否已經開始填寫表格同時也是觸發 Prompt 的時機點。

第二個 props 是 message，也就是要提醒的內容，可以單純帶入一個字串，也可以是一個函式，函式內可以帶入 location 的參數，這樣就能再回傳的內容中加入網址內的資訊。

```js
import { useRef, useState } from "react";
import { Prompt } from "react-router-dom";

const Form = () => {
  const [isEntering, setIsEntering] = useState(false);
  const textInputRef = useRef();

  function submitFormHandler(event) {
    event.preventDefault();

    const enteredText = textInputRef.current.value;
    console.log(enteredText)
  }

  const formFocusedHandler = () => {
    setIsEntering(true);
  }

  const formFinishedHandler = () => {
    setIsEntering(false);
  }
  return (
    <>
      <Prompt
        when={isEntering}
        message={(location) => "Are you sure that you want to leave the page?"}
      />
      <form onFocus={formFocusedHandler} onSubmit={submitFormHandler}>
        <div>
          <label htmlFor="text">Text</label>
          <textarea id="text" rows="5" ref={textInputRef}></textarea>
        </div>
        <button onClick={formFinishedHandler} type="submit">Submit</button>
      </form>
    </>
  );
}
```

## Query Parameters

有時候在產品清單的頁面都會有篩選的功能，篩選完後畫面依舊會是同一個元件，不過呈現的產品項目就會是篩選後的結果，而如果想要把這樣的結果分享出去，我們就可以給定一個特定的網址，而要做到這樣的事就可以使用 Query parameters。

在下方的範例中，我們想達到的功能是當我們點擊 Sort decending 或者 Sort ascending 的按鈕後，網址就會切換成 `our-domain/?sort=des` 或 `our-domain/?sort=asc`。首先我們一樣可以使用 useHistory 來幫助我們進行轉址，不過這裡與上面介紹不同的是，push 也可以帶入物件，物件內會有兩個固定的參數，一個是 pathname，也就是路徑名稱，而另一個是 search，也就是 Query parameters。

接著我們可以使用 useLocation 這個 custom hook 取得目前的網址資訊，例如： pathname 與 search。取得後，我們可以手動去拆分 Query parameters 的內容也可以使用 JavaScript 預設的 class 物件 URLSearchParams 來幫助我們更快速的取得 Query parameters 的值。我們只需使用 `const queryParams = new URLSearchParams(location.search)` 就能賦予 queryParams 一個新的物件，而這個新的物件就有許多方法可以使用，例如： get() 帶入參數就能得到對應的值。

這樣我們就能取得 Query parameters 的值去動態切換按鈕，也可以進一步的去做項目篩選，但這裡主要想介紹的是如何取得 Query parameters 的方法，篩選的邏輯部分就留給大家自己試試。

```js
import { useHistory, useLocation } from 'react-router-dom';

const QuoteList = (props) => {
  const history = useHistory();
  const location = useLocation();

  const queryParams = new URLSearchParams(location.search);
  const isSortingAscending = queryParams.get('sort') === 'asc';

  const changeSortingHandler = () => {
    history.push({
      pathname: location.pathname,
      search: `?sort=${(isSortingAscending?'des':'asc')}`
    })
  }
  return (
    <div>
      <button onClick={changeSortingHandler}>Sort {isSortingAscending?'decending':'ascending'}</button>
    </div>
  );
};

export default QuoteList;
```

## 結語

以上就是針對 React router 額外的一些補充知識，在下篇文章中會為大家繼續介紹 React version 5 如何轉變到 React version 6。感謝大家的閱讀，那我們就下次見囉 🤟🏼
