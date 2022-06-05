---
title: React 學習之旅(20) - React transition group
date: 2022-06-04 20:01:31
tags: [React, React transition group, animation]
categories:
  - React 學習之旅
description: 今天想跟大家介紹一個 React 的套件，它叫做 React transition group。它主要是用來處理 React 的動畫效果，而為什麼需要這個套件來幫助我們處理動畫效果呢？就讓我們一起看下去吧！
---
## 前言

今天想跟大家介紹一個 React 的套件，它叫做 React transition group。它主要是用來處理 React 的動畫效果，而為什麼需要這個套件來幫助我們處理動畫效果呢？就讓我們一起看下去吧！

## 為什麼需要 React transition group?

假設今天我們想要製作一個有動畫效果的 modal，通常我們會使用 state 來控制 modal 是否該顯示，而在 modal 切換的過程中，如果我們想要直接使用 css animation 來讓 modal 在開啟以及關閉時會有淡入以及淡出的效果時，會發現效果不如人意，因為 React 在重新渲染時，並不會等待動畫執行完畢後才渲染，而是立刻完成，所以會有一種像是沒有加入動畫的感覺。

因為這個問題，所以有了 React transition group 這個套件，他可以幫助我們延遲 React 重新渲染的時間，使動畫能夠順利的完成，而該怎麼使用這個套件呢？我們接著看下去！

```js
import React from "react";

import "./Modal.css";

const modal = props => {
  const cssClasses = [
    "Modal",
    props.show ? "ModalOpen" : "ModalClosed"
  ];

  return (
    <div className={cssClasses.join(' ')}>
      <h1>A Modal</h1>
      <button className="Button" onClick={props.closed}>
        Dismiss
      </button>
    </div>
  );
};

export default modal;
```

```css
.ModalOpen {
    animation: openModal 0.4s ease-out forwards;
}

.ModalClosed {
    animation: closeModal 0.4s ease-out forwards;
}

@keyframes openModal {
    0% {
        opacity: 0;
        transform: translateY(-100%);
    }
    50% {
        opacity: 1;
        transform: translateY(90%);
    }
    100% {
        opacity: 1;
        transform: translateY(0);
    }
}

@keyframes closeModal {
    0% {
        opacity: 1;
        transform: translateY(0);
    }
    50% {
        opacity: 0.8;
        transform: translateY(60%);
    }
    100% {
        opacity: 0;
        transform: translateY(-100%);
    }
}
```

## Transition

React transition group 提供了一個元件叫做 Transition 來幫助我們完成延遲渲染。他有兩個重要的 props，分別是 in 以及 timeout。in 的功能是用來判定狀態何時應該切換，就像是我們利用 modalIsOpen 來判斷 Modal 是否開啟一樣。而 timeout 則是用來指定延遲的時間，我們可以直接指定一個數字，用來決定進入和離開的時間，也可以使用一個物件，分別指定兩者的時間。

除了 props 的設定外，我們也需要改寫我們的 JSX。我們必須改寫成函式的方法，並帶入一個參數 state。透過這個 state，我們就能知道目前元件是在進入到離開過程中的哪一個階段，state 的值有四個，分別是：

- entering: 進入中
- entered: 已經進入
- exiting: 離開中
- exited: 已經離開

有了 state，我們就能依據不同的階段給予不同的樣式，除此之外我們還需要設定 mountOnEnter 與 unmountOnExit 才能確保子元件在動畫結束後進行或者取消 mount，當這些都設定好了之後我們的 Modal 就能夠正確的執行動畫效果了。

```js
import React from "react";
import { CSSTransition } from "react-transition-group";

import "./Modal.css";

const animationTiming = {
  enter: 300,
  exit: 1000,
}

const modal = props => {
  return (
    <Transition in={props.show} timeout={animationTiming} mountOnEnter unmountOnExit>
      {state => (
        <div className={['Modal', state === 'entering' ? "ModalOpen" : state === 'exiting'? "ModalClosed" : null].join(' ')}>
          <h1>A Modal</h1>
          <button className="Button" onClick={props.closed}>
            Dismiss
          </button>
        </div>
      )}
    </Transition>
  );
};

export default modal;
```

## CSS Transition

若是想要簡化樣式設定，React transition group 還有提供另一個元件叫做 CSS Transition。我們可以指定一個 class 名稱，而 CSS Transition 就會根據這個名稱為每個階段提供一個預設的 class 名稱。舉例來說，我們設定了 classNames 為 `fade-slide`，**要特別注意個是這裡使用的是 classNames 而不是 className**，我們就可以在 CSS 樣式中針對：

- fade-slide-enter
- fade-slide-enter-active
- fade-slide-exit
- fade-slide-exit-active

這四個 class 名稱去定義元件進入的瞬間、進入的過程、離開的瞬間、離開的過程的樣式。而若是想要自定義樣式名稱，則 classNames 可以改為帶入物件，再依據各個預設的 key 值，輸入自己想要的 class 名稱。

```js
import React from "react";
import { CSSTransition } from "react-transition-group";

import "./Modal.css";

const animationTiming = {
  enter: 300,
  exit: 1000,
}

const modal = props => {
  const customesClassNames = {
    appear: 'my-appear',
    appearActive: 'my-active-appear',
    appearDone: 'my-done-appear',
    enter: 'my-enter',
    enterActive: 'my-active-enter',
    enterDone: 'my-done-enter',
    exit: 'my-exit',
    exitActive: 'my-active-exit',
    exitDone: 'my-done-exit',
  }

  return (
    <CSSTransition in={props.show} timeout={animationTiming} mountOnEnter unmountOnExit classNames="fade-side">
      <div className="Modal">
        <h1>A Modal</h1>
        <button className="Button" onClick={props.closed}>
          Dismiss
        </button>
      </div>
    </CSSTransition>
  );
};

export default modal;
```

```css
.ModalOpen {
    animation: openModal 0.4s ease-out forwards;
}

.ModalClosed {
    animation: closeModal 1s ease-out forwards;
}

.fade-slide-enter-active {
    animation: openModal 0.4s ease-out forwards;
}

.fade-slide-exit-active {
    animation: closeModal 1s ease-out forwards;
}

@keyframes openModal {
    0% {
        opacity: 0;
        transform: translateY(-100%);
    }
    50% {
        opacity: 1;
        transform: translateY(20%);        
    }
    100% {
        opacity: 1;
        transform: translateY(0);        
    }
}

@keyframes closeModal {
    0% {
        opacity: 1;
        transform: translateY(0);
    }
    50% {
        opacity: 0.8;
        transform: translateY(60%);        
    }
    100% {
        opacity: 0;
        transform: translateY(-100%);        
    }
}
```

## TransitionGroup

最後，如果今天元件是一個清單的話，我們就很難指定該依據什麼去判斷清單內的項目該顯示或者移除，也就是設定 Transition 的 in。因此，React transition group 還提供了一個元件就做 Transition Group 來處理清單元件，他能夠在清單項目新增或減少時自動為切換 in 的值，因此我們就不必去煩惱這部分的邏輯了。

而 Transition Group 並不會為元件延遲渲染或者給定預設的樣式名稱，所以我們還是需要使用 Transition 或者 CSSTransition 去幫助我們完成這些任務。除此之外，Transition 可以調整 Html 的標籤，只要修改 component 這個 prop 就可以了，預設是 div。

```js
import React, { Component } from 'react';
import { TransitionGroup, CSSTransition } from 'react-transition-group';

import './List.css';

class List extends Component {
    state = {
        items: [1, 2, 3]
    }

    addItemHandler = () => {
        this.setState((prevState) => {
            return {
                items: prevState.items.concat(prevState.items.length + 1)
            };
        });
    }

    removeItemHandler = (selIndex) => {
        this.setState((prevState) => {
            return {
                items: prevState.items.filter((item, index) => index !== selIndex)
            };
        });
    }

    render () {
        const listItems = this.state.items.map( (item, index) => (
            <CSSTransition key={index} classNames="fade" timeout={300}>
                <li 
                    className="ListItem" 
                    onClick={() => this.removeItemHandler(index)}>{item}</li>
            </CSSTransition>
        ) );

        return (
            <div>
                <button className="Button" onClick={this.addItemHandler}>Add Item</button>
                <p>Click Item to Remove.</p>
                <TransitionGroup component="ul" className="List">
                    {listItems}
                </TransitionGroup>
            </div>
        );
    }
}

export default List;
```

## 結語

以上就是針對 React transition group 的簡單介紹。除了這個套件外，還有 React Motion、 React Move 等等套件也可以幫助處理 CSS animation，但我目前也都還沒有接觸這些套件，所以可能等之後有稍微熟悉了以後再來做個筆記。感謝大家的閱讀，我們下次見囉 🤟🏼
