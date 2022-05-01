---
title: React 學習之旅(14) - Redux (2)
date: 2022-05-01 09:52:23
tags: [React, Redux, Redux Toolkit, side effect, useEffect, action creators]
categories:
- React 學習之旅
description: 在前面的文章有提到，在 Redux 的 reducer 中是不能加入任何 side effect 的，也就是非同步等行為，例如串接 api。但在實際的專案中並不可能都不使用 api，那該如何在 Redux 中處理這些 side effect 呢？就讓我們一起看下去吧！
---
## 前言

在前面的文章有提到，在 Redux 的 reducer 中是不能加入任何 side effect 的，也就是非同步等行為，例如串接 api。但在實際的專案中並不可能都不使用 api，那該如何在 Redux 中處理這些 side effect 呢？就讓我們一起看下去吧！

## useEffect

第一種方法是利用大家已經非常熟悉的 useEffect，我們可以將非同步行為，例如：取得後端資料、送出資料等動作，寫進 useEffect 中。而根據 useEffect 的特性，當我們使用到的值有所改變時，useEffect 就會重新執行，我們就能夠決定發送請求的時機，例如假設有一個產品列表，而我們希望在畫面載入時就發送請求，在 dependencies 中我們就可以不放入任何值，這樣我們就能確保只會在元件生成時發送請求。

以下的範例就是每當元件生成時，就會像後端請求購物車內的資料，若請求成功則發送變更請求將購物車內的資料更新，而若是失敗則發送請求改變訊息提示的內容。

```js
  useEffect(() => {
    const fetchCartData = async () => {
      const response = await fetch('url')
      
      if (!response.ok) {
        throw new Error('Failed to fetch cart data!')
      }
      const data = await response.json();
      return data;
    }
    fetchCartData().then((cartData) =>{
      dispatch(cartActions.replaceCart({
        items: cartData.items || [],
        totalQuantity: cartData.totalQuantity
      }))
    }).catch((error) => {
      dispatch(uiActions.showNotification({
        status:'error',
        title: 'Error!',
        message: 'Failed to fetch data!'
      }))
    })
  },[dispatch])
```

## Action creators

但是當我們將所有非同步的邏輯都寫入元件中時，元件會變得非常龐大，這時我們就可以使用另一個方法 Action creators。
在介紹 Action creators 前，我們先瞭解 thunk 這個概念。 thunk 簡單來說就是一個函式，但他不會馬上執行，而是當再次呼叫時才會執行。如下方的範例中， wrapper_function 回傳的是一個函式 thunk，當我們再次呼叫時，才會執行 thunk 這個函式，並顯示 `Be triggered!'。

```js
function wrapper_function () {
  return function thunk() {
    console.log('Be triggered!')
  }
}

wrapper_function()();  // 'Be triggered!'
```

而在 Redux 中的 dispatch 就運用了這個概念，在先前有提到 dispatch 通常會帶入 `{type:'TYPE', payload:'someValue'}`，而根據傳入的值 reducer 就能進行不同的處理，而其實除了物件以外，dispatch 也能帶入函式。如果 dispatch 內帶入的是函式的話，就會去執行這個函式，而這個函式是以 thunk 的形式去執行的，Redux 會賦予這個函式兩個參數，分別是 dispatch 以及 getState，利用這兩個參數我們就能達到發送請求以及取得最新資料的需求。

所以我們就能夠把剛剛的購物車資料請求改寫成 action creators 的樣子，首先我們定義我們需要呼叫的函式 fetchCartData，並讓這個函式以 thunk 的形式去回傳一個非同步的函式，透過這個非同步的函式我們就能夠先請求資料，再透過 dispatch 這個參數去發送 replaceCart 的請求來變更購物車內的資料，而錯誤時也可以使用 dispatch 去變更提示訊息的內容，而由於我們不會使用到 getState 這個參數，所以就沒有帶入。通常這些 action creators 我們會另外有一個地方做管理，例如： cart-action.js。這樣我們只需要使用時再引入到元件中就可以了，這樣原本的 useEffect 就會非常簡潔，元件也就不會那麼龐大了。

```js
// cart-actions.js
export const fetchCartData = () => {
  return async (dispatch) => {
    const fetchData = async () => {
      const response = await fetch('https://react-http-cf8c0-default-rtdb.firebaseio.com/cart.json')
      
      if (!response.ok) {
        throw new Error('Failed to fetch cart data!')
      }
      const data = await response.json();
      return data;
    }
    try {
      const cartData = await fetchData()
      dispatch(cartActions.replaceCart({
        items: cartData.items || [],
        totalQuantity: cartData.totalQuantity
      }))
    } catch(error) {
      dispatch(uiActions.showNotification({
        status:'error',
        title: 'Error!',
        message: 'Failed to fetch data!'
      }))
    }
  }
}
```

```js
  useEffect(() => {
    dispatch(fetchCart());
  },[dispatch])
```

## 結語

以上就是在使用 Redux 時該怎麼處理非同步事件的方法，感謝大家的閱讀，我們下次見囉 🤟🏼
