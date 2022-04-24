---
title: React 學習之旅(11) - 串接API
date: 2022-04-24 10:44:51
tags: [React, fetch, RESTful API, async/await, try/catch]
categories:
- React 學習之旅
description: 在真實的專案中，資料往往不是存在於本地端，而是遠端的資料庫中。因此需要藉由 API 向後端請求資料或者發送資料，而後端在經過一連串的處理後向資料庫取得資料再回傳到本地端。而今天想跟大家分享的是主要是前半段的步驟，也就是在 React 中如何藉由 API 向後端請求資料或者發送資料，就讓我們一起看下去吧！
---
## 前言

在真實的專案中，資料往往不是存在於本地端，而是遠端的資料庫中。因此需要藉由 API 向後端請求資料或者發送資料，而後端在經過一連串的處理後向資料庫取得資料再回傳到本地端。而今天想跟大家分享的是主要是前半段的步驟，也就是在 React 中如何藉由 API 向後端請求資料或者發送資料，就讓我們一起看下去吧！

## fetch

今天要分享的串接方式是使用現今大多瀏覽器都支援的 fetch api。假如今天是在監聽事件後才會發送請求，我們就可以在監聽的函示內使用 fetch，fetch 可以帶入兩個參數，第一個是 api 的網址，而第二個則是 header，header 又包含請求的方法、挾帶的資料以及一些附加的 header 資訊。預設的情況下是使用 GET，因此若是單純請求資料的話就不需要另外加上 header。在發出請求後會回傳一個 Promise 物件，我們就可以使用 then 去接收成功回傳的資料。但是這時取得的資料是 JSON 的形式，JSON 並不能使用陣列方法，因此我們需要使用 `json()` 將他轉成物件。之後將回傳一個 prmoise 出去再使用 then 去接收結果並使用 useState 中的 setData() 將資料儲存起來，這樣就是一個簡單的請求資料流程。

```js
  function fetchHandler() {
    fetch('url')
      .then((response) => {
        return response.json();
      }).then((data) => {
        setData(data);
      })
  }
```

```js
  function fetchPostHandler() {
    fetch('url', {
      method: 'POST',
      body: JSON.stringify(data),
      headers: {
        'Content-Type':'application/json
      }
    })
      .then((response) => {
        return response.json()
      }).then((data) => {
        console.log(data);
      })
  }
```

### async/await

除了上述的寫法外，我們也可以使用 async/await 的寫法。由於這是一個非同步行為，因此在函式前加上 async，之後我們需要取得 fetch 後得到的 promise 並賦予到 response 這個變數上，所以我們在 fetch() 前加上 await。同樣地，我們需要等待 response 轉化為物件後的 promise 因此也是在 `response.json()` 前加上 await 並賦予到 data 上。以 async/await 的方式撰寫就會簡潔明瞭許多，大家也可以嘗試看看。

```js
  async function fetchHandler() {
    const response = await fetch('url')
    const data = await response.json()

    setData(data);
  }
```

## Loading state

通常在請求資料時，為了增加使用者體驗，都會加上文字或 spinner 的效果讓使用者知道資料正在載入中。就讓我們一起來試著加入 loading 的效果吧。首先，我們可以定義一個 isLoading 的 state，並給予 false 的初始值。在發送請求前，我們將 isLoading 的值設為 true 直到資料儲存後再把 isLoading 設為 false，而這個過程間，我們就可以利用判斷式去決定要顯示的內容。甚至我們還可以加上假如取得的資料長度為零的判斷，這樣就算資料成功取得，但沒有資料，使用者也不會得到空白的畫面了。

```js
  async function fetchHandler() {
    setIsLoading(true);
    const response = await fetch('url')
    const data = await response.json()

    setData(data);
    setIsLoading(false);
  }

  return (
    <section>
      {!isLoading && data.length > 0 && <DataList data={data} />}
      {!isLoading && data.length === 0 && <p>No data found.</p>}
      {isLoading && <p>Data is loading.</p>}
    </section>
  )
```

## Error

剛剛介紹的部分都是成功取得資料的示範，現在來說說如何處理請求失敗。如同 loading 狀態，我們一樣可以定義一個 error 的 state 用來處理錯誤畫面的顯示，不過初始值的部分我們設為 null。在使用 fetch 請求資料時，當錯誤發生時並不會出現 Error 的物件，所以沒辦法直接使用 catch 去接收錯誤訊息，因此我們需要判斷 response.ok 是否為 true，若否則丟出一個錯誤的訊息，這樣我們就能搭配 try/catch 去處理錯誤發生的狀態，而若是想要查看錯誤的狀態碼，也可以使用 `response.status` 做查詢。而在畫面渲染上，就可以再加上 error 是否為 true 的判斷，這樣使用者就能夠知道資料目前是等待中還是請求錯誤或者沒有檔案了。

```js
  async fetchHandler() {
    setError(null)
    setIsLoading(true);
    try {
      const response = await fetch("url");
      if (!response.ok) {
        throw new Error('Something went wrong!')
      }
      const data = await response.json();
      setData(data);
    } catch (error) {
      setError(error.message)
    }
    setIsLoading(false);
  }

  <section>
    {!isLoading && data.length > 0 && <DataList data={data} />}
    {!isLoading && data.length === 0 && !error && <p>No data found.</p>}
    {!isLoading && error && <p>{error}</p>}
    {isLoading && <p>Data is loading.</p>}
  </section>
```

## 搭配 useEffect()

除了在接收到監聽事件然後請求資料外，其實比較常見的做法是當元件生成時就發送請求，而這時就可以使用 useEffect()。我們可以直接將 fetchHandler() 加入到 useEffect() 中，至於 dependencies 的部分，因為有使用到的變數都需要加入 dependencies，所以我們將 fetchHandler 也加入。但這時會有一個問題，就是每次資料重新渲染時，fetchHandler() 都會是一個全新的函示，因此會進入無限循環，而面對這個問題，我們就必須使用之前學到的 useCallback()，稍微改寫了之後，我們就可以在元件創立時取得資料囉！最後，因為 hoisting 的關係記得將 useEffect() 移到 fetchHandler() 下方。

```js
  const fetchHandler() = useCallback(async () => {
    setError(null)
    setIsLoading(true);
    try {
      const response = await fetch("url");
      if (!response.ok) {
        throw new Error('Something went wrong!')
      }
      const data = await response.json();
      setData(data);
    } catch (error) {
      setError(error.message)
    }
    setIsLoading(false);
  }, [])

  useEffect(() => {
    fetchHandler()
  }, [fetchHandler])
```

## 結語

以上就是關於在 React 中如何請求或者發送資料的介紹，以上介紹都是以 RESTful api 為主，至於 GraphQL api 的部分，有機會再跟大家介紹，感謝大家的閱讀，那我們就下次見囉 🤟🏼
