---
title: React 學習之旅(8) - Context
date: 2022-04-19 18:43:34
tags: [React, Context, useContext, Provider, Consumer]
categories:
- React 學習之旅
description: 在 React 中，當我們進行元件間的資料傳遞時，通常使用 props 將父元件中的資料傳遞到子元件中，而若是要資料傳遞到較深層的元件當中，我們就必須要一層層的使用 props 去傳遞資料，而其中經過的元件並未使用到 props 中的資料，只是當作傳遞的媒介，這樣的寫法會使得程式變得十分冗長。幸好 React 提供了 Context 去幫助我們解決這樣的煩惱，就讓我們一起來瞭解 Context 吧！
---
## 前言

在 React 中，當我們進行元件間的資料傳遞時，通常使用 props 將父元件中的資料傳遞到子元件中，而若是要資料傳遞到較深層的元件當中，我們就必須要一層層的使用 props 去傳遞資料，而其中經過的元件並未使用到 props 中的資料，只是當作傳遞的媒介，這樣的寫法會使得程式變得十分冗長。幸好 React 提供了 Context 去幫助我們解決這樣的煩惱，就讓我們一起來瞭解 Context 吧！

## Context

### createContext

首先，我們可以在子目錄中建立 store 的資料夾用於存放我們的 context.js，而這並不是強制的，只是當專案越來越大時，就會有許多 context 的檔案，而這樣集中起來會比較方便管理。

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
└─store
│  │ auth-context.js
│ 
```

context.js 主要是用來建立一個資料存放的空間，我們會使用 createContext 去建立一個物件，而這個物件就可以選擇性地加入預設值，建議有使用到的值還是加上預設值會比較好，因為在使用 VSCode 時，如果有預設值就可以自動完成編輯，能夠減少打字的時間。

```js
// context.js
import React from 'react';

const AuthContext = React.createContext({
  isLoggedIn: false,
});

export default AuthContext;
```

### Provider

每個 Context 物件都有一個名為 Provider 的元件，他主要的功用是用來更新儲存的值。而更新的方法就是使用 value 這個 props 屬性，每當我們利用 Provider 進行值的更新，其他深層的元件也會同步取得最新的結果。而具體實作的方法，可以參考下方的範例，我們先到我們想要傳遞資料的元件中將建立好 context.js 引入，接著 AuthContext.Provider 需要包覆住所有需要傳遞資訊的節點，然後我們就可以使用 value 搭配 {} 動態變更 isLoggedIn 的值。除此之外，context 也是能夠傳遞函式的，因此我們也可以將 logoutHandler 帶入我們的 value 物件中，將他傳入更深層的元件中。

```js
import React, { useState } from "react";

import Login from "./components/Login/Login";
import Home from "./components/Home/Home";
import MainHeader from "./components/MainHeader/MainHeader";
import AuthContext from "./store/auth-context";

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const loginHandler = (email, password) => {
    setIsLoggedIn(true);
  };

  const logoutHandler = () => {
    setIsLoggedIn(false);
  };

  return (
    <AuthContext.Provider value={{ isLoggedIn: isLoggedIn, onLogout: logoutHandler, }}>
      <MainHeader />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler} />}
        {isLoggedIn && <Home onLogout={logoutHandler} />}
      </main>
    </AuthContext.Provider>
  );
}

export default App;
```

### Consumer

接收資料的方法有兩種，第一種是使用 Consumer。 Consumer 跟 Provider 一樣都是 context 物件中提供的元件，而 consumer 的子層需要以函式的形式做撰寫，函式會取得最新的值最為參數並回傳一個 React 的節點，而我們就可以使用這個參數去改寫節點內的內容，具體方法可以參考以下範例，context 就是我們取得的最新資料，有了這筆資料我們就能利用 context.isLoggedIn 進行判斷。除此之外，我們也能從 context 中取得剛剛傳送的 logoutHandler 函式，加入到按鈕中完成登出功能。

```js
import React, {useContext} from 'react';
import AuthContext from '../../store/auth-context';

import classes from './Navigation.module.css';

const Navigation = () => {
  return (
    <AuthContext.Consumer>
      {(context) => {
        return (
          <nav className={classes.nav}>
            <ul>
              {context.isLoggedIn && (
                <li>
                  <a href="/">Users</a>
                </li>
              )}
              {context.isLoggedIn && (
                <li>
                  <a href="/">Admin</a>
                </li>
              )}
              {context.isLoggedIn && (
                <li>
                  <button onClick={context.onLogout}>Logout</button>
                </li>
              )}
            </ul>
          </nav>
        )
      }}
    </AuthContext.Consumer>
  )
};

export default Navigation;
```

### useContext

而接收資料的第二種方法是 useContext，useContext 就像其他 React Hook 一樣在使用前必須從 React 引入，相較於 Consumer， useContext 非常方便，只需要在函式內帶入引入的 context 並賦予到變數上，接著就如同 Consuemr 只要再改寫 React Node 內的資料取得方法就可以了，如： context.isLoggedIn, context.onLogout。

```js
import React {useContext} from 'react';
import AuthContext from '../../store/auth-context';

import classes from './Navigation.module.css';

const Navigation = () => {
  const context = useContext(AuthContext);

  return (
    <nav className={classes.nav}>
      <ul>
        {context.isLoggedIn && (
          <li>
            <a href="/">Users</a>
          </li>
        )}
        {context.isLoggedIn && (
          <li>
            <a href="/">Admin</a>
          </li>
        )}
        {context.isLoggedIn && (
          <li>
            <button onClick={context.onLogout}>Logout</button>
          </li>
        )}
      </ul>
    </nav>
  )
};

export default Navigation;
```

## 結語

雖然 Context 非常實用，但還是存在著一些限制，像是針對高頻率變換的資料就不適合使用 Context 做傳遞，而是會使用 Redux。而什麼是 Redux 會在後續的章節再為大家分享。感謝各位的閱讀，我們下次見！
