---
title: React 學習之旅(4) - 樣式變更
date: 2022-04-14 15:06:56
tags: [React, 行內樣式, className, styled components, css modules, 全域宣告, 區域宣告]
categories:
- React 學習之旅
description: 在之前已經跟大家介紹過在 JSX 語法中是使用 className 代替 class 去指定 component 的樣式，但那時樣式是寫死的，並不能動態切換。今天想跟大家聊聊的是在 React 中如何動態切換樣式，比如說現在有一個表單，當使用者未填寫資料就送出，輸入欄位的外框就會變更顏色，實際要如何操作呢？就讓我們一起來瞭解吧！
---
## 前言

在之前已經跟大家介紹過在 JSX 語法中是使用 className 代替 class 去指定 component 的樣式，但那時樣式是寫死的，並不能動態切換。今天想跟大家聊聊的是在 React 中如何動態切換樣式，比如說現在有一個表單，當使用者未填寫資料就送出，輸入欄位的外框就會變更顏色，實際要如何操作呢？就讓我們一起來瞭解吧！

![動態樣式切換示意圖](https://i.imgur.com/gytDBOd.png)

## 行內樣式

動態切換的方法其實有很多種，第一種是使用行內樣式。行內樣式指的是在標籤中直接加入 style 的屬性，並利用 {} 回傳樣式的屬性以及值，需要特別注意的點是輸入的值為物件，也就是 `{{property: 'value', propertyName: 'value'}}` 的形式，若有使用 - 命名 class 則需要改為小駝峰的風格。以下的範例中，先定義一個 isValid 的變數來儲存表單驗證的情況，預設的 isValid 為 true，當送出表單時，若輸入欄內為空字串，就會賦予 isValid false的值，再藉由三元運算子判斷 isValid，根據 true 或者 false 給定樣式屬性不同的值。而這樣的寫法有一些缺點，第一是行內樣式的權重非常高，很容易不小心蓋過其他樣式的設定，第二是當要改變的樣式很多時，程式碼就會顯得比較雜亂，因此可以使用第二種方法 - className。

```js
import React, { useState } from "react";

import './Input.css';

const Input = (props) => {
  const [enteredValue, setEnteredValue] = useState("");
  const [isValid, setIsValid] = useState(true);

  const goalInputChangeHandler = (event) => {
    if (event.target.value.trim().length > 0) {
      setIsValid(true);
    }
    setEnteredValue(event.target.value);
  };

  const formSubmitHandler = (event) => {
    event.preventDefault();
    if (enteredValue.trim().length === 0) {
      setIsValid(false);
      return;
    }
    console.log('success!');
    setEnteredValue('');
  };
  
  return (
    <form onSubmit={formSubmitHandler}>
      <div className="form-control">
        <label style={{color: isValid?'black':'red'}}>Course Goal</label>
        <input
          style={{borderColor: isValid?'black':'red', background:isValid?'transparent':'salmon'}}
          type="text"
          value={enteredValue}
          onChange={goalInputChangeHandler}
        />
      </div>
      <button type="submit">Add Goal</button>
    </form>
  );
};

export default Input;
```

## className

你可能會想說這不是之前就說過了嗎？沒錯，但之前還沒有加上判斷式，因此樣式是固定的。而要怎麼做才能動態切換呢？首先，我們會進到 css 的檔案並增加同時擁有 form-control 和 invalid class 時的樣式，這樣我們只要透過切換 invalid 就能做到樣式的切換。

```css
.form-control {
  margin: 0.5rem 0;
}

.form-control label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.5rem;
}

.form-control input {
  display: block;
  width: 100%;
  border: 1px solid #ccc;
  font: inherit;
  line-height: 1.5rem;
  padding: 0 0.25rem;
}

.form-control input:focus {
  outline: none;
  background: #fad0ec;
  border-color: #8b005d;
}

.form-control.invalid input {
  border-color: red;
  background: salmon;
}
.form-control.invalid label {
  color: red;
}
```

由於需要加入判斷式，因此需要將 className 的值改為 {}，我們可以使用樣板字面值的方式進行撰寫，在判斷的部分在用 ${} 執行就好，而判斷方法可以使用三元運算子或是之前有提到 || 的特性。這樣子程式碼是不是看起來就簡潔了許多。但以上兩種方式都是直接引入 css 的檔案，雖然是在各個 components 中引入不同的樣式表，但其實這樣的樣式宣告都是全域的，意思是說如果有兩個樣式表共用了 class 名稱，就會發生衝突，權重較低的就會被覆蓋，在小專案時還是比較好管理的，但是到了大專案，可能就會不小心使用了相同的命名，這時就必須使用區域性的樣式變更方法，接下來就讓我們來一一起瞭解吧。

```js
import React, { useState } from "react";

import './Input.css';

const Input = (props) => {
  const [enteredValue, setEnteredValue] = useState("");
  const [isValid, setIsValid] = useState(true);

  const goalInputChangeHandler = (event) => {
    if (event.target.value.trim().length > 0) {
      setIsValid(true);
    }
    setEnteredValue(event.target.value);
  };

  const formSubmitHandler = (event) => {
    event.preventDefault();
    if (enteredValue.trim().length === 0) {
      setIsValid(false);
      return;
    }
    console.log('success!');
    setEnteredValue('');
  };
  
  return (
    <form onSubmit={formSubmitHandler}>
      <div className={`form-control ${isValid || 'invalid'}`}>
        <label>Course Goal</label>
        <input
          type="text"
          value={enteredValue}
          onChange={goalInputChangeHandler}
        />
      </div>
      <button type="submit">Add Goal</button>
    </form>
  );
};

export default Input;
```

## Styled Components

需要區域性的樣式宣告，我們可以使用 styled components 這個套件，安裝上只需要輸入 `npm install --save styled-components` 即可。而環境建立的方式，我們需要從 node_modules 中引入 styled 才能在元件中使用，在引入之後，我們會利用它建立元件，而這個元件只負責處理樣式的部分。而根據不同的 html tag 使用不同的 methods，如以下範例就是建立一個 button 的元件。這時你可能會想 `button`` ` 是什麼妖術，其實這是另一種函式的呼叫方法，叫做 tagged template literal，有興趣的人可以自行研究，這裡只會先介紹使用方法。

```js
import styled from 'styled-components'

const Button = styled.button``
```

而我們在此函式中要帶入的參數就是我們的樣式，樣式撰寫的方法類似於 scss，只不過在本身是不需要使用 css 選擇器的。而樣式的判斷方式則是使用 props 將值傳入到我們建立的 styled components，然後在參數的部分使用 ${} 搭配 arrow function 依據傳入的值回傳判斷後樣式的值。這樣的方法對於喜歡將 CSS 與 JS 分開管理的人來說，可能會覺得很頭痛，因此還有一個方法可以做到區域性的樣式宣告，那就是 css modules。

```js
import React, { useState } from "react";

import styled from "styled-components";
import './CourseInput.css';

const FormControl = styled.div`
  margin: 0.5rem 0;

  & label {
    font-weight: bold;
    display: block;
    margin-bottom: 0.5rem;
    color: ${(props) => props.isValid? 'black':'red'};
  }

  & input {
    display: block;
    width: 100%;
    border: 1px solid ${(props) => props.isValid? 'black':'red'};
    background: ${(props) => props.isValid? 'transparent':'salmon'};
    font: inherit;
    line-height: 1.5rem;
    padding: 0 0.25rem;
  }

  & input:focus {
    outline: none;
    background: ${(props) => props.isValid? '#fad0ec':'salmon'};
    border-color: #8b005d;
  }
`;

const CourseInput = (props) => {
  const [enteredValue, setEnteredValue] = useState("");
  const [isValid, setIsValid] = useState(true);

  const goalInputChangeHandler = (event) => {
    if (event.target.value.trim().length > 0) {
      setIsValid(true);
    }
    setEnteredValue(event.target.value);
  };

  const formSubmitHandler = (event) => {
    event.preventDefault();
    if (enteredValue.trim().length === 0) {
      setIsValid(false);
      return;
    }
    console.log('success!');
    setEnteredValue('');
  };
  
  return (
    <form onSubmit={formSubmitHandler}>
      <FormControl isValid = {isValid}>
        <label>Course Goal</label>
        <input
          type="text"
          value={enteredValue}
          onChange={goalInputChangeHandler}
        />
      </FormControl>
      <button type="submit">Add Goal</button>
    </form>
  );
};

export default CourseInput;
```

## CSS Modules

CSS Modules 若是用於一般的專案中，需要使用 webpack 等工具進行編譯才能轉換為瀏覽器理解的 css，而這一點，若是使用 create-react-app 的方式建立專案，則預設就已經載入編譯器，就可以放心使用 CSS Modules了。而它的使用方法則是需要將原本的 css 檔名加上 module。舉例來說，Input.css 就會更改成 Input.module.css。除此之外，引入的方式也要調整成 `import styles from './Input.module.css'` 而 className 的部分則是需要使用 styles 這個物件去取得對應的 class 名稱。之後再編譯過後，就會是一個獨一無二的 class 名稱，而具體的方式如下：

```js
import React, { useState } from "react";

import styles from './Input.module.css';

const Input = (props) => {
  const [enteredValue, setEnteredValue] = useState("");
  const [isValid, setIsValid] = useState(true);

  const goalInputChangeHandler = (event) => {
    if (event.target.value.trim().length > 0) {
      setIsValid(true);
    }
    setEnteredValue(event.target.value);
  };

  const formSubmitHandler = (event) => {
    event.preventDefault();
    if (enteredValue.trim().length === 0) {
      setIsValid(false);
      return;
    }
    console.log('success!');
    setEnteredValue('');
  };
  
  return (
    <form onSubmit={formSubmitHandler}>
      <div className={`${styles['form-control']} ${isValid || styles.invalid}`}>
        <label>Course Goal</label>
        <input
          type="text"
          onChange={goalInputChangeHandler}
        />
      </div>
      <button type="submit">Add Goal</button>
    </form>
  );
};

export default Input;
```

## 結語

以上就是針對全域、區域樣式宣告以及動態樣式變化的介紹，相信大家在瞭解過後，就能做出更多的使用者互動效果，那我們就下次再見囉！
