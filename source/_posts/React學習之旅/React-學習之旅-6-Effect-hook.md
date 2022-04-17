---
title: React 學習之旅(6) - Effect hook
date: 2022-04-17 17:49:31
tags: [React, Effect hook, side effects, clean-up]
categories:
- React 學習之旅
description: React 主要的任務包含渲染畫面以及當使用者輸入資料時更新畫面，而除此之外的任務，像是利用 localStorage 暫存資料、發送 http 請求以及使用 setTimeout 進行非同步處理，就被稱為 side effects。若是直接將這些 side effects 放入元件中，可能導致無限循環，因為每當資料變更時，就會重新執行一次元件內的指令。而為了解決這個問題，我們就需要使用 Effect hook。而什麼是 Effect hook 呢？就讓我們一起來暸解吧！
---
## 前言

React 主要的任務包含渲染畫面以及當使用者輸入資料時更新畫面，而除此之外的任務，像是利用 localStorage 暫存資料、發送 http 請求以及使用 setTimeout 進行非同步處理，就被稱為 side effects。若是直接將這些 side effects 放入元件中，可能導致無限循環，因為每當資料變更時，就會重新執行一次元件內的指令。而為了解決這個問題，我們就需要使用 Effect hook。而什麼是 Effect hook 呢？就讓我們一起來暸解吧！

## Effect hook

在使用 Effect hook 時，我們主要可以做兩個決定，第一是我們可以決定我們要關注哪些資料，第二是當我們關注的那些資料變動時，我們可以執行什麼動作。藉由這兩個決定，我們就能夠避免元件每次重新渲染時重續執行某些片段，而具體要怎麼做呢？首先，我們需要從 React 引入 useEffect() 這個 hook，接著在元件內使用 useEffect()，並帶入兩個參數，第一個就是我們想執行的動作，以匿名函式的方式輸入，而第二個就是我們想監聽的資料 (dependencies)。

以下為一個簡易的登入畫面，當驗證成功時就會將登入狀態儲存至 localStorage，若沒有使用 useEffect()，會在每次變更資料狀態時，重複判定而進入無限循環。而在使用 useEffect() 後，由於 loggedinstatus 沒有變動，所以只會進行一次判斷。假如監聽資料 (dependencies) 為空陣列的話，則只會在元件建立時執行一次，而若是不給予第二個變數，則每次資料變更時都會執行，就像是沒有使用 useEffect() 一樣。

```js
import React, { useEffect, useState } from 'react';

import Login from './components/Login/Login';
import Home from './components/Home/Home';
import MainHeader from './components/MainHeader/MainHeader';

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const loggedinstatus = localStorage.getItem('isLoggedin');

  useEffect(() => {
    // 若沒有使用 useEffect() 會進入無限循環
    if(loggedinstatus === '1') {
      setIsLoggedIn(true);
    }
  }, [loggedinstatus])

  const loginHandler = (email, password) => {
    localStorage.setItem('isLoggedin','1');
    setIsLoggedIn(true);
  };

  const logoutHandler = () => {
    localStorage.removeItem('isLoggedin');
    setIsLoggedIn(false);
  };

  return (
    <React.Fragment>
      <MainHeader isAuthenticated={isLoggedIn} onLogout={logoutHandler} />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler} />}
        {isLoggedIn && <Home onLogout={logoutHandler} />}
      </main>
    </React.Fragment>
  );
}

export default App;
```

有時我們並不想要使用者在輸入資料後馬上執行動作，例如表單驗證時，我們希望等待個 1 秒後，假如使用者沒有持續輸入才進行驗證。這時我們就可以使用 debouncing 的技巧，將要執行的驗證包覆在 setTimeout() 中，但這時會發現，雖然有延遲，但當每次輸入 Email 或者 Password 時，還是出現了 isValidating 的訊息，這是因為同時間會有許多個 setTimeout() 正在等待，而當時間到了之後，程式還是被執行了。

```js
  useEffect(() => {
    setTimeout(() => {
      console.log('isValidating');
      setFormIsValid(
        enteredEmail.includes('@') && enteredPassword.trim().length > 6
      );
    }, 1000)
  }, [enteredEmail, enteredPassword])
```

這並不是我們預期只出現一次的結果，而要解決這樣的問題，我們就必須加入 cleanup function，它的運作原理是除了元件創建的第一次，每次在執行我們想要執行的 side effects 前，以前面的例子來說的話就是 setTimeout()，還有元件移除前都會執行 cleanup function。而利用這個特性，我們就能夠把上面的例子改寫成：

```js
  useEffect(() => {
    const identifier = setTimeout(() => {
      console.log('isValidating');
      setFormIsValid(
        enteredEmail.includes('@') && enteredPassword.trim().length > 6
      );
    }, 1000)

    return () => {
      console.log('clean-up function');
      clearTimeout(identifier);
    }
  }, [enteredEmail, enteredPassword])
```

改寫後在每次輸入後，都會出現 clean-up function 的訊息，代表我們成功的取消了我們建立的 setTimeout()，只保留了最後一個 setTimeout()，因此只會在最後時才會出現 isValidating 的訊息。

## 結語

以上就是關於 Effect hook 的介紹，Effect hook 的觀念非常重要，因為在使用 React 時勢必會用到許多 side effects，因此熟悉它的使用方法以及原理，相信會幫助大家在開發上的效率。最後，感謝大家的閱讀，那我們就下次見囉！
