---
title: React 學習之旅(12) - Custom hook
date: 2022-04-25 13:00:36
tags: [React, Custom hook, fetch]
categories:
- React 學習之旅
description: 在專案中，總是會有一些邏輯是重複的，而在使用原生 JavaScript 撰寫時，我們可以將重複的邏輯包裝成一個函式然後輸出並引入到我們需要的檔案中。但是在 React 中， React Hooks 在元件裡不能被其他函式所包覆，因此若是重複的邏輯中有使用到 React Hooks，那麼原本的方法就沒辦法使用了，這時我們就必須使用 Custom hook，而什麼是 Custom hook 呢？就讓我們一起來瞭解吧！
---
## 前言

在專案中，總是會有一些邏輯是重複的，而在使用原生 JavaScript 撰寫時，我們可以將重複的邏輯包裝成一個函式然後輸出並引入到我們需要的檔案中。但是在 React 中， React Hooks 在元件裡不能被其他函式所包覆，因此若是重複的邏輯中有使用到 React Hooks，那麼原本的方法就沒辦法使用了，這時我們就必須使用 Custom hook，而什麼是 Custom hook 呢？就讓我們一起來瞭解吧！

## Custom hook

Custom hook 其實就是一個自定義的函式，通常會在 src 底層額外建立一個 hooks 的資料夾存放各式各樣的 Custom hooks。而 Custom hooks 與其他函式不一樣的點在於它的命名必須是以 use 作為開頭，根據這個 use，React 就能判定這是一個 Custom hook 而不是一個元件或者一般的函式。

在 Custom hook 中，我們可以使用我們之前學到的各種 React Hooks，例如： useState、useEffect 和 useCallback。除此之外，我們還能夠引入參數去增加 custom hooks 的變化性，而參數可以是一般的值也可以是函式，就像是 useEffect 一樣。同樣地，如同在使用 useState 時，我們可以從回傳的值中解構出 state 和修改 state 的函式，我們也可以在 Custom hook 的最後回傳我們想要取得的值，而這並不是強制的，你也可以不回傳任何值。最後，引入的方式就如同 React hooks 一樣，若是有回傳值則可以賦予到變數上做後續的使用。

```js
  // use-demo.js
  const useDemo = (para) => {
    // 共用的邏輯
    return 'something';
  }
  
  export default useDemo
```

```js
// App.js
import useDemo from './hooks/use-demo';

function App() {
  const something = useDemo();

  return (
    // JSX
  )
};
```

### 範例 - 串接 API

接下來，會示範如何將串接 API 包裝成一個 Custom hook 以方便在處理多個 API 時，不用重複撰寫一樣的程式碼。首先，我們可以針對共用的部分進行撰寫。在發送請求時，我們都會希望資料開始讀取時會有 loading 的效果，同樣地，資料取得失敗時會有錯誤的訊息，所以我們利用 useState 定義了 isLoading 和 error，並在開始、錯誤發生與結束時給予相對應的值。而在最後因為元件中需要使用到 isLoading、 error 和 sendRequest，所以我們需要將三個變數以物件的方式回傳，這樣在元件中我們就能夠用解構的方式取得對應的變數。

```js
import { useState } from "react";

const useFetch = () => {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const sendRequest = async () => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch();

      if (!response.ok) {
        throw new Error('Request failed!');
      }

      const data = await response.json();
    } catch (err) {
      setError(err.message || 'Something went wrong!');
    }
    setIsLoading(false);
  }

  return { isLoading, error, sendRequest}
}

export default useFetch;
```

處理完串接 API 中共同的部分後，我們可以針對不同的部分利用參數做變化。首先，由於每個 API 可能會有不同的 url、method、body 與 headers，因此我們想要在第一個參數中得到這些資訊，所以我們將之命名為 requestConfig 並希望是以物件的形式傳入。通常使用 GET 去發送請求時，只會輸入 url 而已，因此我們可以預設當 method, body 和 headers 不存在時，就是以 GET 的方式去發送請求。除了 requestConfig 會有差異外，當我們在請求成功並獲得 data 時，也會進行不一樣的處理方式，這時候就能以函式的方式傳入，而我們把它命名為 applyData 並且將取得的 data 作為參數在傳入到 applyData 中，這樣就完成了變數的部分囉！

```js
import { useState } from "react";

const useFetch = (requestConfig,applyData) => {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const sendRequest = async () => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch(requestConfig.url, {
        method: requestConfig.method? requestConfig.method: 'GET',
        body: requestConfig.body? JSON.stringify(requestConfig.body) : null,
        headers: requestConfig.headers?requestConfig.headers: {},
      });

      if (!response.ok) {
        throw new Error('Request failed!');
      }

      const data = await response.json();
      applyData(data);
    } catch (err) {
      setError(err.message || 'Something went wrong!');
    }
    setIsLoading(false);
  }

  return { isLoading, error, sendRequest}
}

export default useFetch;
```

通常我們會使用 useEffect 在元件建立時就發送請求取得資料，這時就必須將發送請求的函式加入到 dependencies 中，而我們已經在前面的文章介紹過，每次元件重新執行時，都會建立一個全新的函式，因此會讓元件進入無限循環，而為了解決這個問題就必須使用 useCallback，所以我們就可以把 sendRequest 加上 useCallback，這樣即使是用 useEffect 也不會發生問題了。

```js
import { useState, useCallback } from "react";

const useFetch = (requestConfig,applyData) => {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const sendRequest = useCallback(async () => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch(requestConfig.url, {
        method: requestConfig.method? requestConfig.method: 'GET',
        body: requestConfig.body? JSON.stringify(requestConfig.body) : null,
        headers: requestConfig.headers?requestConfig.headers: {},
      });

      if (!response.ok) {
        throw new Error('Request failed!');
      }

      const data = await response.json();
      applyData(data);
    } catch (err) {
      setError(err.message || 'Something went wrong!');
    }
    setIsLoading(false);
  },[])

  return { isLoading, error, sendRequest}
}

export default useFetch;
```

## 結語

以上就是針對 Custom hook 的簡單介紹，其實 Custom hook 還可以使用在許多不同的實際案例中，這就留給大家自己嘗試看看囉！感謝大家的閱讀，我們下次見 🤟🏼
