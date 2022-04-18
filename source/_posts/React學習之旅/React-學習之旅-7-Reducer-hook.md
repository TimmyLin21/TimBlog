---
title: React 學習之旅(7) - Reducer hook
date: 2022-04-18 17:07:39
tags: [React, Reducer hook, useReducer]
categories:
- React 學習之旅
description: 之前在處理資料時，都是使用 state 來進行管理，但是有時候面對比較複合性的資料，舉例來說有多種 state，且使用不同的方法進行資料變更或者資料彼此之間有著相依性。這種情況下單純使用 state 也是可以的，但是就會讓程式碼顯得比較冗長，此時就可以考慮使用 Reducer hook，它提供我們更強大的狀態管理方法，而具體要怎麼操作呢？就讓我們一起看下去吧！
---
## 前言

之前在處理資料時，都是使用 state 來進行管理，但是有時候面對比較複合性的資料，舉例來說有多種 state，且使用不同的方法進行資料變更或者資料彼此之間有著相依性。這種情況下單純使用 state 也是可以的，但是就會讓程式碼顯得比較冗長，此時就可以考慮使用 Reducer hook，它提供我們更強大的狀態管理方法，而具體要怎麼操作呢？就讓我們一起看下去吧！

## Reducer Hook

首先我們需要從 React 引入 useReducer，`import {useReducer} from 'react'`，接著我們可以先瞭接 useReducer() 的結構。如同 useState()， useReducer() 會回傳一個陣列並包含兩個值，第一個值是資料狀態的快取，也就是元件在重新渲染後所取得的資料，而第二個值則是一個函式，用來發送一個動作，而通常這個動作(action)就是用來進行資料的更新。而 useReducer() 還會帶入三個參數，分別是 reducer function，初始狀態以及初始函式。Reducer function 會在每次發送動作(action)時自動執行，並會帶入最新的快取資料狀態以及 action 作為參數。需注意的是， reducer function 最後必須回傳更新後的狀態(state)。初始函式則是當初始狀態較為複雜需要進行特別處理時才會使用。

![useReducer()](https://i.imgur.com/qJj5hRH.png)

瞭解了 useReducer() 之後，我們可以從一個實際的案例中來看看如何使用。以下為一個簡易的登入頁面，使用者在輸入信箱以及密碼後可以成功登入，但有進行簡易的驗證，信箱的部分需要在字串中加上 @，而密碼的部分則最少需要 6 個字元。信箱以及密碼都有加入監聽方式 onChange 以及 onBlur，除止之外搭配 useEffect() 減少表單重複驗證，只會在使用者停止輸入的後 0.5 秒進行驗證。以下是使用 useState() 進行資料狀態管理。

```js
import React, { useState, useEffect } from 'react';

import Card from '../UI/Card/Card';
import classes from './Login.module.css';
import Button from '../UI/Button/Button';

const Login = (props) => {
  const [enteredEmail, setEnteredEmail] = useState('');
  const [emailIsValid, setEmailIsValid] = useState();
  const [enteredPassword, setEnteredPassword] = useState('');
  const [passwordIsValid, setPasswordIsValid] = useState();
  const [formIsValid, setFormIsValid] = useState(false);

  useEffect(() => {
    console.log('EFFECT RUNNING');

    return () => {
      console.log('EFFECT CLEANUP');
    };
  }, []);

  useEffect(() => {
    const identifier = setTimeout(() => {
      console.log('Checking form validity!');
      setFormIsValid(
        enteredEmail.includes('@') && enteredPassword.trim().length > 6
      );
    }, 500);

    return () => {
      console.log('CLEANUP');
      clearTimeout(identifier);
    };
  }, [enteredEmail, enteredPassword]);

  const emailChangeHandler = (event) => {
    setEnteredEmail(event.target.value);
  };

  const passwordChangeHandler = (event) => {
    setEnteredPassword(event.target.value);
  };

  const validateEmailHandler = () => {
    setEmailIsValid(enteredEmail.includes('@'));
  };

  const validatePasswordHandler = () => {
    setPasswordIsValid(enteredPassword.trim().length > 6);
  };

  const submitHandler = (event) => {
    event.preventDefault();
    props.onLogin(enteredEmail, enteredPassword);
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <div
          className={`${classes.control} ${
            emailIsValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor="email">E-Mail</label>
          <input
            type="email"
            id="email"
            value={enteredEmail}
            onChange={emailChangeHandler}
            onBlur={validateEmailHandler}
          />
        </div>
        <div
          className={`${classes.control} ${
            passwordIsValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor="password">Password</label>
          <input
            type="password"
            id="password"
            value={enteredPassword}
            onChange={passwordChangeHandler}
            onBlur={validatePasswordHandler}
          />
        </div>
        <div className={classes.actions}>
          <Button type="submit" className={classes.btn} disabled={!formIsValid}>
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
};

export default Login;
```

而要將以上狀態管理從 useState() 改成 useReducer()，首先解構出信箱以及密碼的資料快取以及發送函示並給予初始值 `{value: '', isValid: null}`。而我們可以將 reducer function 以具名函式的方式傳入，我們先預設回傳的結果值為空字串、驗證結果為 false。

```js
const emailReducer = (state, action) => {
  return { value: "", isValid: false };
};

const passwordReducer = (state, action) => {
  return { value: "", isValid: false };
};

const [emailState, dispatchEmail] = useReducer(emailReducer, {
  value: "",
  isValid: null,
});

const [passwordState, dispatchPassword] = useReducer(passwordReducer, {
  value: "",
  isValid: null,
});
```

接著，我們可以在監聽函式內加上 dispatch function，也就是更新方法並帶入參數(action)。action 是一個物件包含了觸發的方式以及值，而透過從 action 取得的觸發方式，我們就可以為 reducer function 加入判斷，根據不同的情況，回傳不一樣的結果。由於 reducer 內的 state 參數為最新的快取，因此 onBlur 的資料更新就不需要特地帶入值，只需要取用 state 中的值就可以了。

```js
const emailReducer = (state, action) => {
  if (action.type === 'EMAIL_INPUT') {
    return { value: action.val, isValid: action.val.includes("@")}
  } else if (action.type === 'EMAIL_BLUR') {
    return { value: state.value, isValid: state.value.includes("@")}
  }
  return { value: "", isValid: false };
};

const passwordReducer = (state, action) => {
  if (action.type === 'PASSWORD_INPUT') {
    return { value: action.val, isValid: action.val.trim().length > 6}
  } else if (action.type === 'PASSWORD_BLUR') {
    return { value: state.value, isValid: state.value.trim().length > 6}
  }
  return { value: "", isValid: false };
};

const emailChangeHandler = (event) => {
  dispatchEmail({type: 'EMAIL_INPUT', val: event.target.value})
};

const passwordChangeHandler = (event) => {
  dispatchPassword({type: 'PASSWORD_INPUT', val: event.target.value})
};

const validateEmailHandler = () => {
  dispatchEmail({type: 'EMAIL_BLUR'})
};

const validatePasswordHandler = () => {
  dispatchPassword({type: 'PASSWORD_BLUR'})
};
```

最後我們需要修改 useEffect() 的部分，由於 emailState 和 passwordState 包含了值與驗證結果，但我們並不想要每次值改變時就進行整個表格驗證，而是信箱或者密碼的驗證結果改變才進行整個表格的驗證。所以我們就必須把 emailValid 和 passwordValid 解構出來。而解構後我們就可以只監聽這兩者的值變化再去進行驗證的動作，大幅地減低了不必要的驗證次數。

```js
const {isValid: emailValid} = emailState;
const {isValid: passwordValid} = passwordState;

useEffect(() => {
  const identifier = setTimeout(() => {
    console.log('Checking form validity!');
    setFormIsValid(
      emailValid && passwordValid
    );
  }, 500);

  return () => {
    console.log('CLEANUP');
    clearTimeout(identifier);
  };
}, [emailValid, passwordValid]);
```

以上就是將 useState() 轉換為 useReducer 的方法，而完整的程式碼可以查看下方。

```js
import React, { useState, useEffect, useReducer } from "react";

import Card from "../UI/Card/Card";
import classes from "./Login.module.css";
import Button from "../UI/Button/Button";

const emailReducer = (state, action) => {
  if (action.type === 'EMAIL_INPUT') {
    return { value: action.val, isValid: action.val.includes("@")}
  } else if (action.type === 'EMAIL_BLUR') {
    return { value: state.value, isValid: state.value.includes("@")}
  }
  return { value: "", isValid: false };
};

const passwordReducer = (state, action) => {
  if (action.type === 'PASSWORD_INPUT') {
    return { value: action.val, isValid: action.val.trim().length > 6}
  } else if (action.type === 'PASSWORD_BLUR') {
    return { value: state.value, isValid: state.value.trim().length > 6}
  }
  return { value: "", isValid: false };
};

const Login = (props) => {
  const [formIsValid, setFormIsValid] = useState(false);

  const [emailState, dispatchEmail] = useReducer(emailReducer, {
    value: "",
    isValid: null,
  });

  const [passwordState, dispatchPassword] = useReducer(passwordReducer, {
    value: "",
    isValid: null,
  });

  const {isValid: emailValid} = emailState;
  const {isValid: passwordValid} = passwordState;

  useEffect(() => {
    const identifier = setTimeout(() => {
      console.log('Checking form validity!');
      setFormIsValid(
        emailValid && passwordValid
      );
    }, 500);

    return () => {
      console.log('CLEANUP');
      clearTimeout(identifier);
    };
  }, [emailValid, passwordValid]);

  const emailChangeHandler = (event) => {
    dispatchEmail({type: 'EMAIL_INPUT', val: event.target.value})
  };

  const passwordChangeHandler = (event) => {
    dispatchPassword({type: 'PASSWORD_INPUT', val: event.target.value})
  };

  const validateEmailHandler = () => {
    dispatchEmail({type: 'EMAIL_BLUR'})
  };

  const validatePasswordHandler = () => {
    dispatchPassword({type: 'PASSWORD_BLUR'})
  };

  const submitHandler = (event) => {
    event.preventDefault();
    props.onLogin(emailState.value, passwordState.value);
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <div
          className={`${classes.control} ${
            emailState.isValid === false ? classes.invalid : ""
          }`}
        >
          <label htmlFor="email">E-Mail</label>
          <input
            type="email"
            id="email"
            value={emailState.value}
            onChange={emailChangeHandler}
            onBlur={validateEmailHandler}
          />
        </div>
        <div
          className={`${classes.control} ${
            passwordState.isValid === false ? classes.invalid : ""
          }`}
        >
          <label htmlFor="password">Password</label>
          <input
            type="password"
            id="password"
            value={passwordState.value}
            onChange={passwordChangeHandler}
            onBlur={validatePasswordHandler}
          />
        </div>
        <div className={classes.actions}>
          <Button type="submit" className={classes.btn} disabled={!formIsValid}>
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
};

export default Login;
```

## 結語

雖然 useReducer() 是一個強大的狀態管理工具，但是主要的狀態管理還是以 useState() 為主，若是資料的相依性過高才需要考慮需不需要使用 useReducer()。若是每個狀態都要改成 useReducer() 則有點殺雞用牛刀的感覺。以上就是對 Reducer hook 的介紹，那我們就下次見囉！
