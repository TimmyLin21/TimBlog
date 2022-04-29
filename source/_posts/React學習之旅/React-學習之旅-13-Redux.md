---
title: React 學習之旅(13) - Redux
date: 2022-04-28 19:35:53
tags: [React, Redux, Redux Toolkit]
categories:
- React 學習之旅
description: 當資料需要進行跨元件傳遞時，我們可以使用 Context，不過 Context 並不適合過度頻繁的資料變更。除此之外，Context 在大型專案管理上設定起來比較複雜，因此我們可以使用第三方套件 - Redux 幫助我們完成這個任務，而什麼是 Redux 呢？就讓我們一起來瞭解吧！
---
## 前言

當資料需要進行跨元件傳遞時，我們可以使用 Context，不過 Context 並不適合過度頻繁的資料變更。除此之外，Context 在大型專案管理上設定起來比較複雜，因此我們可以使用第三方套件 - Redux 幫助我們完成這個任務，而什麼是 Redux 呢？就讓我們一起來瞭解吧！

## 基本介紹

雖然 Redux 廣泛使用在 React 專案中，但它不僅僅侷限於 React 中，也可以使用在原生 JavaScript 的專案中。Redux 的核心概念是將資料統一管理在一個地方，而這些資料不能直接修改，而是必須透過 reducer function 進行變更。而這個 reducer function 與 useReducer 中的 reducer function 類似，都是 pure function，當給定特定的參數時，就能獲得特定的值且不受外在因素影響，例如：函式內不能使用 fetch 請求資料。

既然有修改資料的方法，那當然也有取得資料的方式。在 Redux 中，會使用 subscribe 的方式綁定到元件上，每當資料變更時，就會自動更新資料。

而有了取得以及修改的方法，我們還需要一個執行的動作，這個動作會由元件發起 (dispatch)，發起後就會執行 reducer function，進而形成一個循環，具體的過程可以參考下方的動畫。

![Redux flow](https://d33wubrfki0l68.cloudfront.net/01cc198232551a7e180f4e9e327b5ab22d9d14e7/b33f4/assets/images/reduxdataflowdiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif)

## 在原生 JavaScript 使用 Redux

以下會先以原生 JavaScript 的環境示範 Redux 是如何運作的，之後會再介紹在 React 的環境該如何設定。

首先我們可以使用 `import redux from 'redux'` 引入 redux。這樣我們就可以使用 redux 的方法 createStore 建立集中管理資料的地方。

這個 createStore 必須帶入一個參數，也就是我們剛剛提到的修改方法 reducer function。如同 useReducer 中的 reducer function，需要帶入兩個參數，第一個是舊的值，而一開始 state 為 undefined，所以我們必須賦予第一個參數一個預設值，以避免發生錯誤。而第二個則是 action，用來判斷該進行哪個動作，記住 reducer function 若是要進行資料變更，都是**回傳一個新的物件**，而不是直接修改原本的資料，這是因為物件傳參考的特性可能會不小心更改到不想更改的地方，因此才需要覆蓋原本的物件。

```js
const store = redux.createStore(reducerFunction);

const reducerFunction = (state = { defaultKey: defaultValue}, action) => {
  if (action.type === 'action1') {
    return {
      defaultKey: state.defaultKey + 1
    }
  } else if (action.type === 'action2') {
    return {
      defaultKey: state.defaultKey - 1
    }
  }
  return state;
}
```

這樣我們就設定好我們的 store 和 reducer function 了，接下來我們可以定義取得的方法。我們可以利用 store 提供的 subscribe()，每當資料變更時，就會執行帶入的函式。而在這個函式內我們又可以再使用 store 的 getState() 取得最新的資料。這樣每當資料更新後，就會自動在 console 中列出最新的值囉！

```js
const subscripter = () => {
  const latestState = store.getState();
  console.log(latestState);
}

store.subscribe(subscripter);
```

最後，由於我們沒有建立元件，所以我們直接使用 store 的 dispatch() 發送請求，由於我們剛剛訂閱了資料變更，所以我們就能得到兩次最新的 state。這就是 Redux 在原生 Javascript 運作的簡易示範。

```js
store.dispatch({type:'action1'});
store.dispatch({type:'action2'});
```

## 在 React 使用 Redux

由於 Redux 不是只有運行在 React 中，所以如果要在 React 中使用的話需要使用 `npm install react-redux` 額外安裝 react-redux 才能使用一些方便在 React 中開發的功能。
如同 Context，在使用 Redux 時會在 src 底層建立一個 store 的資料夾來管理 Redux 的資料。

```markdown
<!-- 目錄結構 -->
project  
│
│
└─src
│  │ App.js
│  │ index.js
│  │ ....
│ 
└─store
│  │ index.js
│ 
```

以下會使用一個簡易的計數器做示範，在 React 中 store 與 reducer function 的設定沒有太大差異，只是在最後需要將建立好的 store 輸出，這樣我們才能在其他元件中使用。另外 action 的部分通常會是一個物件，而第一個 key 值為 type，利用其值去判斷要做什麼動作，而除了判斷的條件外，也能帶入其他 key 去改變值，例如在 `action.type === "increase"` 時，就會依照 action.amount 帶入的值去做改變。

```js
import { createStore } from "redux";

const counterReducer = (state = { counter: 0, counterShow: false }, action) => {
  if (action.type === "increment") {
    return {
      counter: state.counter + 1,
      counterShow: state.counterShow
    };
  } else if (action.type === "decrement") {
    return {
      counter: state.counter - 1,
      counterShow: state.counterShow
    };
  } else if (action.type === "increase") {
    return {
      counter: state.counter + action.amount,
      counterShow: state.counterShow
    };
  } else if (action.type === "toggle") {
    return {
      counter: state.counter,
      counterShow: !state.counterShow
    }
  }
  return state;
};

const store = createStore(counterReducer);

export default store;
```

通常為了確保整個 React app 都能取得 store 內的資料，我們會在 index.js 內使用 `import { Provider } from 'react-redux' 引入 Provider，而這個 Provider 就跟 Context 類似，提供被包覆的元件能夠獲取資料。不過我們還必須告訴 Provider 我們要分享的是哪一個 store，因此將剛剛輸出的 store 引入到 index.js 中並帶入 store 的屬性中，這樣我們就能確保每個元件都能取得資料囉！

```js
// index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux'

import './index.css';
import App from './App';
import store from './store/index'

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Provider store={store}><App /></Provider>);
```

接下來，我們就可以到元件中去設定取得的方式以及發送的方式。我們分別用 useSelector 和 useDispatch 這兩個 react-redux 提供的 custom hooks 去完成取得資料以及發送。useSelector() 必須帶入一個函式，告知 store 我們目前要取得是哪一個值，而之後它就會回傳我們指定的值並自動幫我們訂閱，也就是說我們可以省略掉剛剛在原生 JavaScript 訂閱的步驟。而 useDispatch() 則會回傳一個函式，而我們就能將這個函式放入 eventHandler 中去發送不同的變更請求。

```js
import { useSelector, useDispatch } from 'react-redux';

import classes from './Counter.module.css';

const Counter = () => {
  const dispatch = useDispatch();
  const counter = useSelector(state => state.counter);

  const incrementHandler = () => {
    dispatch({ type: 'increment' });
  };

  const decrementHandler = () => {
    dispatch({ type: 'decrement' });
  };

  const toggleCounterHandler = () => {};

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      <div className={classes.value}>{counter}</div>
      <div>
        <button onClick={incrementHandler}>Increment</button>
        <button onClick={decrementHandler}>Decrement</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

## Redux toolkit

當專案越來越大時，我們集中管理的資料也會越來越多，這時程式碼就會變得很冗長，我們就可以使用另一個套件 Redux toolkit 去幫助我們將資料分門別類。

安裝方式若是使用 npm 可以輸入 `npm install @reduxjs/toolkit`。而使用的方式會跟 Redux 有一點不一樣，在 Redux toolkit 中會利用 createSlice() 分別為不同類別的資料建立 reducers，之後再利用 configureStore() 合併成一個 reducer。

以下就是將管理計數的 reducer 與管理登入的 reducer 合併的範例，其中 createSlice 中的 key 值是固定不能自行定義的，例如: name, initialState 和 reducers。前面我們有提到 reducer 必須回傳一個全新的物件不能直接修改原本的值，但是這在 Redux toolkit 是可以的，因為 Redux toolkit 會自動為我們比對並建立一個全新的物件回傳，所以我們就只需要更改我們需要的值就可以了。除此之外，可以發現 reducers 內也少了 state.type 的判斷，這也是因為 Redux toolkit 幫我們在背後處理了這件事，在等等介紹發送時會多做說明。

在 configureStore() 內會帶入一個物件，裡面會有一個 key 值為 **reducer**，這裡是單數，因為會合併成一個，對應的值通常也是一個物件，這時我們就可以寫入我們剛剛用 createSlice() 建立的 reducer。

比較特別的是在 Redux toolkit 中，我們也必須將各個 slice 的 actions 也輸出到需要使用的元件中，這跟剛剛提到自動判斷 state.type 也是息息相關的。

```js
import { createSlice, configureStore } from "@reduxjs/toolkit";

const initialCounterState = {
  counter: 0,
  counterShow: false,
};

const counterSlice = createSlice({
  name: "counter",
  initialState: initialCounterState,
  reducers: {
    increment(state) {
      state.counter++;
    },
    increase(state, action) {
      state.counter += action.payload;
    },
    decrement(state) {
      state.counter--;
    },
    toggle(state) {
      state.counterShow = !state.counterShow;
    },
  },
});

const initialAuthState = {
  isAuthenticated: false,
};
const authSlice = createSlice({
  name: "auth",
  initialState: initialAuthState,
  reducers: {
    login(state) {
      state.isAuthenticated = true;
    },
    logout(state) {
      state.isAuthenticated = false;
    },
  },
});

const store = configureStore({
  reducer: { counter: counterSlice.reducer, auth: authSlice.reducer },
});

export const counterActions = counterSlice.actions;
export const authActions = authSlice.actions;
export default store;
```

在元件中一樣是使用 useSelector 和 useDispatch 來完成資料請求與發送資料變更請求，只是帶入的值會有些差異。在 useSelector() 中，由於現在的 state 有分成 counter 與 auth 所以我們必須在 state 中指定 couter 的物件才能取得 counter 內的值。而 useDispatch 的部分，我們會帶入 counterActions 中的其中一個方法，例如：`counterAction.increase(5)`。這時回傳出來的是一個 action 物件，`{type: '自動生成的辨識碼', payload: 5}`，而這個自動生成的辨識碼就是 Redux toolkit 判斷要執行哪個動作的關鍵。除此之外，我們帶入的參數都會自動帶入 payload 的值中，而這個 payload 是固定的，因此若需要比較複雜的判斷，可以帶入物件，再去 reducer 內進行操作。

```js
import classes from './Counter.module.css';
import { useSelector, useDispatch, connect } from 'react-redux';
import { counterActions } from '../store/index';

const Counter = () => {
  const dispatch = useDispatch();
  const toggleCounterHandler = () => {
    dispatch(counterActions.toggle())
  };
  const counter = useSelector(state => state.counter.counter);
  const counterShow = useSelector(state => state.counter.counterShow);

  const incrementHandler = () => {
    dispatch(counterActions.increment())
  }
  const decrementHandler = () => {
    dispatch(counterActions.decrement())
  }
  const increaseHandler = () => {
    dispatch(counterActions.increase(5))
  }

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      {counterShow && <div className={classes.value}>{counter}</div>}
      <div>
        <button onClick={incrementHandler}>Increment</button>
        <button onClick={increaseHandler}>Increase by 5</button>
        <button onClick={decrementHandler}>Decrement</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};
```

現在，Redux toolkit 已經可以正常運作了，但我們還可以再稍微管理一下 index.js 內的程式碼。我們可以分別將 authSlice 和 counterSlice 分別移到 auth.js 和 counter.js 中，這樣 index.js 就只要負責引入 reducer 做合併的動作就好了，看起來就相對乾淨且好管理。

```js
// auth.js
import { createSlice } from '@reduxjs/toolkit';

const initialAuthState = {
  isAuthenticated: false,
};

const authSlice = createSlice({
  name: 'authentication',
  initialState: initialAuthState,
  reducers: {
    login(state) {
      state.isAuthenticated = true;
    },
    logout(state) {
      state.isAuthenticated = false;
    },
  },
});

export const authActions = authSlice.actions;

export default authSlice.reducer;
```

```js
// counter.js
import { createSlice } from '@reduxjs/toolkit';

const initialCounterState = { counter: 0, showCounter: true };

const counterSlice = createSlice({
  name: 'counter',
  initialState: initialCounterState,
  reducers: {
    increment(state) {
      state.counter++;
    },
    decrement(state) {
      state.counter--;
    },
    increase(state, action) {
      state.counter = state.counter + action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

export const counterActions = counterSlice.actions;

export default counterSlice.reducer;
```

```js
// index.js
import { configureStore } from '@reduxjs/toolkit';

import counterReducer from './counter';
import authReducer from './auth';


const store = configureStore({
  reducer: { counter: counterReducer, auth: authReducer },
});

export default store;
```

```markdown
<!-- 目錄結構 -->
project  
│
│
└─src
│  │ App.js
│  │ index.js
│  │ ....
│ 
└─store
│  │ auth.js
│  │ counter.js
│  │ index.js
│ 
```

## 結語

以上就是針對 Redux 與 Redux toolkit 的基本介紹，在實作上 Redux 與 Context 並不是二選一而已，也是可以同步使用的，可以針對適合的情境做選擇。感謝大家的閱讀，那我們就下次見囉！
