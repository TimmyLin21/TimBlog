---
title: React 學習之旅(10) - class-based Components
date: 2022-04-22 11:51:15
tags: [React, functional components, class-based components, life-cycle, error boundaries]
categories:
- React 學習之旅
description: 在 React 中元件建立的方法有兩種，一種是在前面的章節中利用函式去建構的 functional components，而另一種則是今天想跟大家分享的 class-based components。而什麼是 class-based components 又為什麼要使用它呢？就讓我們一起看下去吧！
---
## 前言

在 React 中元件建立的方法有兩種，一種是在前面的章節中利用函式去建構的 functional components，而另一種則是今天想跟大家分享的 class-based components。而什麼是 class-based components 又為什麼要使用它呢？就讓我們一起看下去吧！

## Class-based components 是什麼？

顧名思義，class-based components 就是利用 class 語法去建立的元件。我們利用 class 這個語法糖去定義一個建構子函式，然後繼承 Component，這樣我們就能使用 React 所提供的各種方法了。以下是一個簡易的示範用兩種方式建構出來的元件會分別長什麼樣子，如果是 functional components 我們只需要在最後 return JSX 就可以了，而 class-based components 則需要繼承 Component 的方法 render() 才能將 JSX 交付給 React 處理，而後面還會再詳細介紹如何將 functional components 的各種hooks 轉換為 class-based components。

```js
// functional components
function Product() {
  return <h2>A Product!</h2>
}
```

```js
// class-based components
class Product extends Component {
  render() {
    return <h2>A Product!</h2>
  }
}
```

## 為什麼要使用 class-based components?

在 React 16.8 以前並沒有 React Hooks，像是 useState, useEffect 等等功能。因此若是想使用這些方法就必須使用 class-based components 去繼承 component 中的方法才能使用。然而現在的 React 已經有 hooks 能夠更加方便地完成這些事，所以逐漸地 functional components 變成了主流的寫法。但目前 Error Boundaries 的功能還是只有 class-based components 可以完成，而什麼是 Error Boundaries 會在之後為大家做介紹，因此使用 class-based components 比較像是需求取向，可能是公司的專案大部分還是使用 class-based components 或者如剛剛提到需要使用 error boundaries 又或者個人偏好，不然應該還是 functional components 為主。

## 改寫 functional components

說了這麼多，就讓我們一起來改寫 functional components 吧！

### props

剛剛已經介紹過要如何改寫 JSX 的部分，現在我們來看看 props 該如何改寫。在 class-based components 中我們不需要帶入 props 的參數，因為 props 已經存放在繼承的 Component 中，因此我們只需要使用 this.props 就能取得 props 中的屬性了喔，是不是很簡單啊！

```js
import { Component } from 'react';

class User extends Component {
  render() {
    return <div>{this.props.name}</div>;
  }
}

export default User;
```

### useState

接著我們來改寫最常使用到的 useState。首先我們必須在 constructor() 內定義 this.state 的初始值，**初始值的部分必須是物件**，由於是繼承自 Component 所以必須加上 super()，除此之外 state 是固定的，不像是 useState 我們可以定義自己喜歡的名稱。而變更的方法則是使用 this.setState()，一樣帶入的值也必須是物件，下方是確保取得的 state 為最新值的寫法。另外需要注意的點是**更新時並不會取代原本的物件而是只會更新你提供的屬性而已**。以下方的例子來說，如果有 showUsers 以外的屬性，在點擊 toggleUsersHandler 後，state 內還是會存在其他屬性。

```js
import { Component } from 'react/cjs/react.production.min';
import User from './User';

class Users extends Component {
  constructor() {
    super();
    this.state = {
      showUsers: true,
    }
  }

  toggleUsersHandler = () => {
    this.setState((curState) => {
      return {
        showUsers: !curState.showUsers
      }
    });
  };

  render() {
    // ...
  }
}

export default Users;
```

### onEvent = {}

在呼叫事件觸發的方法時，需要使用 this 去取得元件內我們定義的方法，而除此之外還有一個小細節需要注意，就是必須綁定 this。這是因為 this 是取決於怎麼被呼叫的，所以今天我們使用 onClick 去觸發的話， this 就會指向 button，因此我們需要使用 bind() 將 this 改為指定成元件本身。這樣就能正確的呼叫 toggleUsersHandler 了。

```js
  <button onClick={this.toggleUsersHandler.bind(this)}>
```

### useEffect

在 class-based components 中，通常是以生命週期的角度去看待一個元件，生命週期指的是元件從建立到銷毀的過程。常見的生命週期包含了 componentDidMount()、componentDidUpdate() 和 componentWillUnmount()，這三個生命週期分別定義了元件在**綁定到DOM後**、**更新後**以及**銷毀前**應該執行哪些事情。而這其實跟 useEffect() 非常相似，當今天沒有帶入任何 dependencies 時，useEffect 就只會在建立後執行一次這就跟 componentDidMount() 一樣。而假設今天我們給定了 dependencies，那 useEffect 就會在 dependencies 變動時重新執行，而這也就跟 componentDidUpdate() 所做的事情非常類似。最後在 useEffect 中我們會使用 clean-up function 來指定重新執行前應該做什麼事情，這也跟 componentWillUnmount() 有所關聯。所以我們就可以使用這三個生命週期來改寫 useEffect。

![life-cyle vs useEffect](https://i.imgur.com/nH6uUcS.png)

首先我們先來改寫成 componentDidMount()，基本上只需要將 useEffect 內會執行的程式片段移到 componentDidMount 內就可以了，記得將 functional components 的寫法改為 class-based components，例如 state 的更新。

```js
  useEffect(() => {
    setTimeout(() => {
      setColorState('wheat');
    }, 2000);
  },[])
```

```js
  componentDidMount() {
    setTimeout(() => {
      this.setState({ color: 'wheat' });
    }, 2000);
  }
```

接下來的例子是當使用者輸入關鍵字後，會從 USERS 這個固定的使用者名單中去篩選出包含關鍵字的使用者。我們試著將他改寫成 componentDidUpdate()，假如我們跟剛剛一樣只是將程式碼全部複製到 componentDidUpdate() 中，就會形成無限循環。這是因為當 state 改變了之後，又會進入元件更新完成的狀態，所以又再一次更新直到永遠。為了解決這個問題，我們就必須加入判斷式，componentDidUpdate 中可以加入兩個參數，分別是更新前的 props 以及更新前的 state，而我們就可以取用更新前的 state 去跟現在的 state 做比較，假如不一樣我們就執行篩選的步驟，假如一樣則不動作，這樣就可以避免無限循環囉！

```js
  useEffect(() => {
    setFilteredUsers(
      USERS.filter((user) => user.name.includes(searchTerm))
    );
  }, [searchTerm]);
```

```js
  componentDidUpdate(prevProps, prevState) {
    if (prevState.searchTerm !== this.state.searchTerm) {
      this.setState({
        filteredUsers: USERS.filter((user) => user.name.includes(this.state.searchTerm))
      });      
    }
  }
```

而 componentWillUnmount() 與 componentDidMount() 類似，只要將 clean-up function 內需要執行的片段放到 componentWillUnmount() 中並注意改寫成 class-based components 的寫法就好了。

### Context

Context 的部分一樣是使用 createContext 建立共用的資料，而 Provider 和 Consumer 也是相同的使用方法，而唯一不同的點在於 useContext。在 class-based component 中如果想要使用 useContext 的方法，可以使用 `static contextType = 引入的 Context`，而取得 context 裡的資料的方法一樣是使用 `this.context.屬性名稱`，但需要特別注意的是這個方法只能使用一個 context。假如需要兩個不同來源的 context 的話，就必須先合併成一個之後在引入。

```js
import { Fragment, Component } from 'react';

import Users from './Users';
import classes from './UserFinder.module.css';
import UsersContext from '../store/users-context';

class UserFinder extends Component {
  static contextType = UsersContext;
  constructor() {
    super()
    this.state = {
      filteredUsers: [],
      searchTerm: '',
    }
  };

  componentDidMount() {
    this.setState({ filteredUsers: this.context.users });
  }

  render() {
    return (
      // ....
  };
}

export default UserFinder;
```

## Error Boundaries

以上就是 functional components 改寫成 class-based components 的方法，而之前有提到 class-based components 不同於 functional components 的一點是前者具有設定 Error Boundaries 的功能，那 Error Boundaries 到底是什麼呢？它其實就是用來設定當錯誤發生時該執行什麼事情的機制，有點像是使用 JavaScript 撰寫 try 和 catch 的感覺。而具體來說該怎麼做呢？首先我們可以建立一個 ErrorBoundary 的元件，他就像是 UI components 可以包覆住其他元件，之後再利用 componentDidCatch(error) 這個生命週期，我們可以在元件發生錯誤時將 state 中的 hasError 變更為 true，而只要 hasError 為 true 就回傳一個錯誤的畫面給使用者，這樣使用者就不會因為系統錯誤而得到一片空白的畫面了。

```js
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor() {
    super();
    this.state = { hasError: false };
  }

  componentDidCatch(error) {
    console.log(error);
    this.setState({ hasError: true });
  }

  render() {
    if (this.state.hasError) {
      return <p>Something went wrong!</p>;
    }
    return this.props.children;
  }
}

export default ErrorBoundary;
```

## 結語

以上就是對於 class-based  components 的介紹，其實兩者是可以共同使用的，不過這樣的使用情境應該是不太常見的，而至於要使用哪一種方法建構元件就看個人或者公司專案的喜好囉！感謝大家的閱讀，那我們就下次見 🤟🏼
