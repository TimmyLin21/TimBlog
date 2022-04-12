---
title: 'React 學習之旅(2) - Event, State'
date: 2022-04-12 20:17:03
tags: [React, Event, State, 雙向綁定, 子傳父]
categories:
- React 學習之旅
description: 前面已經分享了 React 的基礎介紹、 Component 和 JSX。今天想分享的是 React 如何處理事件監聽以及如何使用 State 進行資料的儲存以及變更。透過這兩個概念，我們就能夠讓 Component 做更多互動的效果，就讓我們一起來暸解吧！
---
## 前言

前面已經分享了 React 的基礎介紹、 Component 和 JSX。今天想分享的是 React 如何處理事件監聽以及如何使用 State 進行資料的儲存以及變更。透過這兩個概念，我們就能夠讓 Component 做更多互動的效果，就讓我們一起來暸解吧！

![監聽器回來了](https://i.imgur.com/18h0efw.gif)

## Event

在使用純 JS 綁定監聽時，我們需要先取得 DOM 上的節點，然後指定監聽的方法以及監聽觸發後的函式。而在 React 中，不需要那麼麻煩，我們只需要在想要監聽的 HTML tag 上加上 `on` 並配上監聽方法，如： `onClick`、`onChange` 等等，之後指定監聽觸發後的函式，如： `clickHandler`，就能達到監聽的效果。而這裡要注意的是觸發的函式請不要加上()，因為若加上()，React 在第一次渲染時，就會執行函式，而之後就算監聽事件觸發了，也不會再執行函式。

```js
// 純 JS
const btn = document.querySelector('.btn');
btn.addEventListener('click', ()=>{});

// React
const clickHandler = () => {};
<button onClick={clickHandler}>Click Me</button>

```

而有了觸發函式，你可能會想這樣我就可以進行值的變更了，於是你定義了一個變數 title，希望當點擊觸發時， title 就會從變成 New Title。但事與願違，雖然 console 的結果中， title 的確變成了 New Title，可是畫面上渲染的結果還是 title。會發生這樣的原因是 React 其實只有渲染了一次畫面，就算你的值已經變更過了，還是不會渲染到畫面上，而要解決這個問題，就必須使用 state。

```js
import React from 'react';

function Component() {
  let title = 'title';
  
  const changeHandler = () => {
    title = 'New Title';
    console.log(title);
  }

  return (
    <div>
      <h2>{ title }</h2>
      <button onClick = { changeHandler }>Change Title</button>
    </div>
  )
}

export default Component;
```

## State

在使用 state 前，記得從 react 的資料庫中，將 useState 引入進來。useState 這個函式可以帶入一個參數，而這個參數就是初始值。而執行 useState 時，會回傳一個陣列，裡面包含兩個值，分別是目前的值以及一個函式。通常，我們會使用解構賦值的方式處理回傳的陣列。而這個函式非常重要，它可以幫助我們儲存想要變更的值並且重新渲染畫面，透過這個函式我們才可以達到我們想要的效果。

```js
import React, {useState} from 'react';

function Component() {
  // 解構賦值
  const [title, setTitle] = useState('title');
  
  const changeHandler = () => {
    // 儲存值並重新渲染
    setTitle('New Title');
  }

  return (
    <div>
      <h2>{ title }</h2>
      <button onClick = { changeHandler }>Change Title</button>
    </div>
  )
}

export default Component;
```

而今天當我們遇到需要儲存許多值的狀況時，有另外兩種方式可以處理 useState。第一種方法是以物件的型式去儲存我們需要的值，之後再利用展開運算子的方式去覆蓋舊的值。但是這個方法有一個問題，就是當值變多時，因為 React 的機制並不會馬上就渲染，而會等待需要更新的量到達一個批量才進行處理，這時取得的值就不一定會是最新的。這時就可以使用第二種方法，確保取得的值是最新的。

```js
import React, {useState} from 'react';

function Component() {
  const [data, setData] = useState({
    title: 'title',
    subtitle: 'subtitle',
  });
  
  const titleChangeHandler = () => {
    setData({
      ...data,
      title: 'New Title',
    });
  }

  const subTitleChangeHandler = () => {
    setData({
      ...data,
      subtitle: 'New Subtitle',
    });
  }

  return (
    <div>
      <h1>{data.title}</h1>
      <button type="text" onClick={titleChangeHandler}>change title</button>
      <h2>{data.subtitle}</h2>
      <button type="text" onClick={subTitleChangeHandler}>change subtitle</button>
    </div>
  )
}

export default Component;
```

第二種方法是在變更的函式中，也就是 setData 中，使用一個回呼函式 (callback function) 並帶入一個參數，而這個參數就能確保取得的是最新更新的值，然後就能再次使用展開運算子的方式去覆蓋舊的值。這樣我們就能確保每次更新都是最新的值囉！

```js
import React, {useState} from 'react';

function Component() {
  const [data, setData] = useState({
    title: 'title',
    subtitle: 'subtitle',
  });
  
  const titleChangeHandler = () => {
    setData((prevState) => {
      return {...prevState, title: 'New Title'}
    })
  }

  const subTitleChangeHandler = () => {
    setData((prevState) => {
      return {...prevState, subtitle: 'New Subtitle'}
    })
  }

  return (
    <div>
      <h1>{data.title}</h1>
      <button type="text" onClick={titleChangeHandler}>change title</button>
      <h2>{data.subtitle}</h2>
      <button type="text" onClick={subTitleChangeHandler}>change subtitle</button>
    </div>
  )
}

export default Component;
```

## 雙向綁定

而透過 Event 和 state 的搭配，我們還能夠使用表單元素，例如：input，select 等等，進行雙向綁定。也就是當我輸入值的時候，資料能夠同步渲染到畫面上。相同地，我們在 input 上綁定 change 這個監聽事件，每當我輸入值的時候就會觸發事件，此時我們在使用與純 JS一樣的手法，利用 event 去取得 input 的值，並且使用 React 的更新函式進行變更與渲染。而最後記得在 input 上加上 `value={title}`，這樣當 title 的值變更時，input的值也會跟著改變。

```js
import React, {useState} from 'react';

function Component() {
  const [title, setTitle] = useState('title');
  
  const titleChangeHandler = (e) => {
    setTitle(e.target.value);
  }

  return (
    <div>
      <h1>{title}</h1>
      <input type="text" onChange={titleChangeHandler} value={title} />
    </div>
  )
}

export default Component;
```

## 資料傳遞 - 子傳父

在先前的文章中，我們已經介紹過使用 Props 可以達到父元件傳遞資料到子元件的功能，現在我們可以使用 Event 和 State 達到另一種傳遞方式，也就是子元件傳遞到父元件。首先，我們在父層的子元件標籤上新增一個自定義的事件，這個事件會帶入一個參數用來更新父元件的值。之後，以 Props 的方式將自定義的事件傳入子元件中，而當我們想要將值從子元件傳遞到父元件，只需要將值帶入參數中，就能觸發父元件的更新動作，以下有一個簡易的示範。

```js
import React, {useState} from 'react';
import ChildComponent from './ChildComponent';

function Component() {
  const [title, setTitle] = useState('title');
  const changeTitleHandler = (newTitle) => {
    setTitle(newTitle);
  }

  return (
    <div>
      <h1>{title}</h1>
      <ChildComponent onChangeTitle = {changeTitleHandler} />
    </div>
  )
}

export default Component;
```

```js
import React, {useState} from 'react';

function ChildComponent(props) {
  const [title, setTitle] = useState('title');
  
  const titleChangeHandler = (e) => {
    setTitle(e.target.value);
    props.onChangeTitle(e.target.value);
  }

  return (
    <div>
      <input type="text" onChange={titleChangeHandler} value={title} />
    </div>
  )
}

export default ChildComponent;
```

## 結語

以上就是針對 Event, State, 雙向綁定 以及 子傳父的學習心得，那我們就下次見囉！
