---
title: React 學習之旅(19) - 實作登入系統
date: 2022-05-26 23:27:06
tags: [React, Authentication, Firebase, Navigator guard, local storage]
categories:
  - React 學習之旅
description: 這一篇將利用 React 搭配 Firebase 實作一個簡易的登入系統，這個系統期望能夠利用信箱註冊帳號密碼，註冊完成就可以在登入後變更使用者密碼，除此之外，我們還希望瀏覽器能夠記得我們已經登入並且在一定的時間後自動登出，而究竟要如何實作呢？就讓我們繼續看下去吧！
---

## 前言

這一篇將利用 React 搭配 Firebase 實作一個簡易的登入系統，這個系統期望能夠利用信箱註冊帳號密碼，註冊完成就可以在登入後變更使用者密碼，除此之外，我們還希望瀏覽器能夠記得我們已經登入並且在一定的時間後自動登出，而究竟要如何實作呢？就讓我們繼續看下去吧！

## 建立基本結構

首先，我們先來架設登入系統的基本結構，基本上會有三個頁面，分別是首頁、登入頁以及使用者資料頁。首頁的功能很單純就只是顯示 Hellow World! 而已。

```js
const Home = () => {
  return <h1>Hellow World!</h1>;
};

export default Home;
```

而登入頁除了登入外還可以透過點擊按鈕切換成註冊新帳號的模組。

```js
import { useState } from "react";

const Login = () => {
  const [isLogin, setIsLogin] = useState(true);

  const loginToggleHandler = () => {
    setIsLogin((prevState) => !prevState);
  };

  return (
    <>
      <h1>{isLogin ? "登入" : "註冊"}</h1>
      <form>
        <label htmlFor="loginEmail">信箱</label>
        <br />
        <input type="email" id="loginEmail" />
        <br />
        <label htmlFor="loginPassword">密碼</label>
        <br />
        <input type="password" id="longinPassword" />
        <br />
        <button type="submit">{isLogin ? "登入" : "註冊"}</button>
        <button type="button" onClick={loginToggleHandler}>
          {isLogin ? "註冊新帳號" : "登入現有帳號"}
        </button>
      </form>
    </>
  );
};

export default Login;
```

最後，使用者頁面則是在登入後可以進行密碼的變更，而在一開始只是先將其架構先做出來而已。

```js
const User = () => {
  return (
    <form>
      <label htmlFor="keyword">新密碼</label>
      <br />
      <input id="keyword" type="text" min="7" placeholder="最少七碼" />
      <button type="submit">變更密碼</button>
    </form>
  );
};

export default User;
```

三個頁面的切換就會透過 React Router 來達成，當我們點擊 Logo 就會前往首頁，點擊登入則會進入登入頁面，當然地，點擊使用者資料就會進入使用者頁面，而除此之外，還有一個登出按鈕，目前還沒有實際作用。

```js
import { NavLink, Link } from "react-router-dom";

const Header = () => {
  return (
    <header>
      <Link to="/">Logo</Link>
      <nav>
        <ul>
          <li>
            <NavLink to="/login">登入</NavLink>
          </li>
          <li>
            <NavLink to="/user">使用者資料</NavLink>
          </li>
          <li>
            <button type="button">登出</button>
          </li>
        </ul>
      </nav>
    </header>
  );
};

export default Header;
```

```js
import { Routes, Route } from "react-router-dom";
import Header from "./components/Header";

import Home from "./pages/Home";
import Login from "./pages/Login";
import User from "./pages/User";

export default function App() {
  return (
    <>
      <Header />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/user" element={<User />} />
        <Route path="/login" element={<Login />} />
      </Routes>
    </>
  );
}
```

就這樣，這個登入系統的基本架構就算是完成了，接著就可以開始著手各項功能的實踐了。

## 註冊新帳號

要開始實作註冊新帳號的功能前，我們必須先到 Firsebase 開啟一個新的專案，並從專案簡介取得 Web API key，這樣我們才能透過 Firebase 的 RESTful API 註冊新帳號。

![Firsebase Web API key](https://i.imgur.com/HnLjEZJ.png)
![Firsebase signup doc](https://i.imgur.com/SIzshd7.png)

有了 Web API Key 之後，我們就可以串接 Firebase 的 API 並利用 useRef 取得使用者輸入的信箱與密碼，並加入到發送請求的 body 中，就這樣按照 Firebase doc 的要求，我們成功的註冊了第一個會員信箱與密碼。

```js
import { useState, useRef } from "react";

const Login = () => {
  const [isLogin, setIsLogin] = useState(true);

  const emailInput = useRef();
  const passwordInput = useRef();

  const loginToggleHandler = () => {
    setIsLogin((prevState) => !prevState);
  };

  const submitHandler = (e) => {
    e.preventDefault();

    const enteredEmail = emailInput.current.value;
    const enteredPassword = passwordInput.current.value;
    
    fetch(
      "https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=[API_key]",
      {
        method: "POST",
        body: JSON.stringify({
          email: enteredEmail,
          password: enteredPassword,
          returnSecureToken: true
        }),
        headers: {
          "Content-type": "application/json"
        }
      }
    )
      .then((res) => {
        return res.json();
      })
      .then((data) => {
        console.log(data);
      });
  };

  return (
    <>
      <h1>{isLogin ? "登入" : "註冊"}</h1>
      <form onSubmit={submitHandler}>
        <label htmlFor="loginEmail">信箱</label>
        <br />
        <input type="email" id="loginEmail" ref={emailInput} />
        <br />
        <label htmlFor="loginPassword">密碼</label>
        <br />
        <input type="password" id="longinPassword" ref={passwordInput} />
        <br />
        <button type="submit">{isLogin ? "登入" : "註冊"}</button>
        <button type="button" onClick={loginToggleHandler}>
          {isLogin ? "註冊新帳號" : "登入現有帳號"}
        </button>
      </form>
    </>
  );
};

export default Login;
```

## 登入帳號

有了會員帳號密碼，現在我們就可以測試另一個 API，也就是登入的 API，由於 headers 和 body 是相同的，我們可以將剛剛的請求做一些調整，這樣我們就能根據 isLogin 來判斷目前是要登入還是註冊了。而除此之外，我們還需要儲存回傳的 idToken 和 expiresIn，這樣一方面我們才能知道使用者是否已經登入，而另一方面則是知道何時應該要登出。

![signIn API](https://i.imgur.com/sAJDufc.png)

```js
  const submitHandler = (e) => {
    e.preventDefault();

    const enteredEmail = emailInput.current.value;
    const enteredPassword = passwordInput.current.value;

    let url;
    if (!isLogin) {
      url =
        "https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=[API_key]";
    } else {
      url =
        "https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=[API_key]";
    }
    console.log(url);
    fetch(url, {
      method: "POST",
      body: JSON.stringify({
        email: enteredEmail,
        password: enteredPassword,
        returnSecureToken: true
      }),
      headers: {
        "Content-type": "application/json"
      }
    })
      .then((res) => {
        return res.json();
      })
      .then((data) => {
        const {idToken, expiresIn} = data;
        console.log(idToken,expiresIn);
      });
  };
```

## 共享登入資訊

有了 idToken 和 expiresIn 之後，由於 headers、userPage 都會使用到這些資料，所以我們需要利用 Context 或者 Redux 來集中管理資料。這裡會使用前者當作示範。在 AuthContext 中會存放 token ，這樣我們才能使用他在之後進行使用者的密碼變更，除此之外，還會有 isLoggedIn 來幫助我們調整 Header 顯示的項目，最後還會有登入以及登出的方法。

```js
import { createContext, useState } from "react";

const AuthContext = createContext({
  token: null,
  isLoggedIn: false,
  login: (token) => {},
  logout: () => {}
});

export const AuthContextProvider = (props) => {
  const [token, setToken] = useState(null);
  const userIsLoggedIn = !!token;

  const loginHandler = (token) => {
    setToken(token);
  };

  const logoutHandler = () => {
    setToken(null);
  };

  const contextValue = {
    token,
    isLoggedIn: userIsLoggedIn,
    login: loginHandler,
    logout: logoutHandler
  };

  return (
    <AuthContext.Provider value={contextValue}>
      {props.children}
    </AuthContext.Provider>
  );
};

export default AuthContext;
```

有了 isLoggedIn 我們就可以為我們的 Header 加上一些判斷式，我們希望使用者在未登入前不會看到使用者資料以及登出的選項，另外登入後也不會在看到登入的選項。

```js
import { NavLink, Link } from "react-router-dom";
import { useContext } from "react";

import AuthContext from "../store/auth-context";

const Header = () => {
  const authCtx = useContext(AuthContext);

  const logoutHandler = () => {
    authCtx.logout();
  };

  return (
    <header>
      <Link to="/">Logo</Link>
      <nav>
        <ul>
          {!authCtx.isLoggedIn && (
            <li>
              <NavLink to="/login">登入</NavLink>
            </li>
          )}
          {authCtx.isLoggedIn && (
            <li>
              <NavLink to="/user">使用者資料</NavLink>
            </li>
          )}
          {authCtx.isLoggedIn && (
            <li>
              <button type="button" onClick={logoutHandler}>
                登出
              </button>
            </li>
          )}
        </ul>
      </nav>
    </header>
  );
};

export default Header;
```

## Navigator guard

但是光是隱藏了連結選項，使用者還是可以手動輸入連結網址，所以我們還要加上 Navigator guard，去防止使用者在未登入前直接輸入網址到特定的網頁。加入的方式很簡單，就是加入判斷式，當 isLoggedIn 為 true 時，若輸入 `/login` 使用者會因為沒有 `/login` 的路徑而自動跳轉到首頁，若為 false 時，使用者就能前往 `/login`。同樣的方法也套用在 `/user` 上，只不過在 false 的時候，希望跳轉的畫面改為登入畫面。

```js
import { Routes, Route, Navigate } from "react-router-dom";
import { useContext } from "react";
import Header from "./components/Header";

import Home from "./pages/Home";
import Login from "./pages/Login";
import User from "./pages/User";

import AuthContext from "./store/auth-context";

export default function App() {
  const authCtx = useContext(AuthContext);

  return (
    <>
      <Header />
      <Routes>
        <Route path="/" element={<Home />} />
        {!authCtx.isLoggedIn && <Route path="/login" element={<Login />} />}
        <Route
          path="/user"
          element={
            authCtx.isLoggedIn ? (
              <User />
            ) : (
              <Navigate to="/login" replace={true} />
            )
          }
        />
        <Route path="*" element={<Navigate to="/" replace={true} />} />
      </Routes>
    </>
  );
}

```

## Local storage 儲存 token

這樣使用者已經可以正常的創建帳號、登入以及登出，但我們還希望瀏覽器能夠記得使用者已經登入過了，這時我們就可以使用 local storage 來存取我們取得的 token。在登入時使用 localStorage.setItem 將 token 儲存起來，而在重新整理後，我們就能透過 localStorage.getItem 取得我們的 token 並將它設為預設值。而最後在登出時，記得也要被儲存的 token 給清除。

```js
import { createContext, useState } from "react";

const AuthContext = createContext({
  token: null,
  isLoggedIn: false,
  login: (token) => {},
  logout: () => {}
});

export const AuthContextProvider = (props) => {
  const initialToken = localStorage.getItem("token");
  const [token, setToken] = useState(initialToken);
  const userIsLoggedIn = !!token;

  const loginHandler = (token) => {
    setToken(token);
    localStorage.setItem("token", token);
  };

  const logoutHandler = () => {
    setToken(null);
    localStorage.removeItem("item");
  };

  const contextValue = {
    token,
    isLoggedIn: userIsLoggedIn,
    login: loginHandler,
    logout: logoutHandler
  };

  return (
    <AuthContext.Provider value={contextValue}>
      {props.children}
    </AuthContext.Provider>
  );
};

export default AuthContext;
```

## 自動登出

最後，我們還剩下 expiresIn 還沒有處理，它可以幫助我們決定自動登出的時間點，但我們不能直接取用，因為回傳的值是秒數，我們必須將時間轉換為 ISO String 以方便後面做使用。

```js
  const { idToken, expiresIn } = data;
  const expirationTime = new Date(new Date().getTime() + +expiresIn *1000);
  AuthCtx.login(idToken, expirationTime.toISOString());
```

因為我們已經將時間字串化，所以我們也可以將它跟 token 一樣存放進 local storage。除此之外，我們可以利用取得的過期時間與目前的時間做比較，如果小於零，表示這個 token 已經過期了，也就沒有儲存的必要，就可以清除 local storage，反之，則取得儲存的 token  和時間。

```js
const caculateRemainingTime = (expirationTime) => {
  const currentTime = new Date().getTime();
  const adjExpirationTime = new Date(expirationTime).getTime();

  const remainingTime = adjExpirationTime - currentTime;
  return remainingTime;
}
```

```js
const retrieveStoredToken = () => {
  const storedToken = localStorage.getItem("token");
  const storedExpirationTime = localStorage.getItem("expirationTime")

  const remainingTime = caculateRemainingTime(storedExpirationTime);
  if ( remainingTime <= 0) {
    localStorage.removeItem('token');
    localStorage.removeItem('expirationTime');
    return null;
  }
  return {
    token: storedToken,
    duration: remainingTime,
  };
}
```

有了剩餘的時間就能設定 setTimeout 在指定的時間點執行 logoutHandler，而在執行 logoutHandler 時記得要清除我們設定的 logoutTimer。而若是剩餘的時間是來自於 local storage，我們就需要使用 useEffect 來幫助我們在一開始 tokenData 變更後設定自動清除的時間，由於有使用到 logoutHandler，所以必須為 logoutHandler 加上 useCallback()。

```js
let logoutTimer;

export const AuthContextProvider = (props) => {
  const tokenData = retrieveStoredToken();
  let initialToken;

  if (tokenData) {
    initialToken = tokenData.token;
  }
  const [token, setToken] = useState(initialToken);
  const userIsLoggedIn = !!token;

  const logoutHandler = useCallback(() => {
    setToken(null);
    localStorage.removeItem("token");
    localStorage.removeItem("expirationTime");

    if (logoutTimer) {
      clearTimeout(logoutTimer);
    }
  }, []);

  useEffect(() => {
    if (tokenData) {
      logoutTimer = setTimeout(logoutHandler, tokenData.duration);
    }
  }, [tokenData, logoutHandler]);

  const loginHandler = (token, expirationTime) => {
    setToken(token);
    localStorage.setItem("token", token);
    localStorage.setItem("expirationTime", expirationTime);

    const remainingTime = caculateRemainingTime(expirationTime);
    logoutTimer = setTimeout(logoutHandler, remainingTime);
  };

  const contextValue = {
    token,
    isLoggedIn: userIsLoggedIn,
    login: loginHandler,
    logout: logoutHandler
  };

  return (
    <AuthContext.Provider value={contextValue}>
      {props.children}
    </AuthContext.Provider>
  );
};

export default AuthContext;
```

## 結語

最後總算是完成這個簡易的登入系統了，雖然功能看似十分陽春，但使用了非常多之前學習到的觀念，例如： state、props 和context 等等。總結來說，算是一個不錯的練習呢，大家有空的話也可以玩玩看，那我們就下次見囉 🤟🏼
