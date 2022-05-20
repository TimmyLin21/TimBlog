---
title: React 學習之旅(18) - 發佈 SPA 到 Firebase
date: 2022-05-20 13:26:07
tags: [React, SPA, Firebase, lazy loading]
categories:
  - React 學習之旅
description: 當一個專案完成之後，我們需要將專案發佈到網頁伺服器，這樣別人才能直接輸入網址瀏覽我們製作的網頁。而網頁伺服器的選擇有很多，例如： Firebase、AWS 或者 Github。本次會介紹的是使用 Firebase 部署的方法，因為 Firebase 再處理 Single Page Application 上比較簡單，就讓我們看下去吧！
---
## 前言

當一個專案完成之後，我們需要將專案發佈到網頁伺服器，這樣別人才能直接輸入網址瀏覽我們製作的網頁。而網頁伺服器的選擇有很多，例如： Firebase、AWS 或者 Github。本次會介紹的是使用 Firebase 部署的方法，因為 Firebase 再處理 Single Page Application 上比較簡單，就讓我們看下去吧！

## 優化

### Lazy loading

在此之前，我們還可以使用 Lazy loading 的方法針對網頁的載入速度稍微進行一些優化。Lazy loading 使我們在進入網頁後不會馬上下載所有路徑底下的資料，而是當網址切換到指定的路徑時，才會載入需要的資料。這樣的好處是，使用者並不一定會瀏覽所有的路徑，若在一開始就傳輸所有的資料，一方面會延遲載入的速度，另一方面也浪費了這些傳輸量。

而要怎麼實踐 Lazy loading 呢？ 首先我們需要改寫元件引入的方式，改為以函式的形式引入，如： `const Component = React.lazy(() => import('path'));`，這樣只有在輸入特定網址時，才會開始載入相對應的元件了。除此之外，我們還可以使用 Suspense 這個元件，定義載入的期間該呈現什麼，讓使用者知道目前正在載入中。我們只需將 Suspense 包覆整個 Switch 並在 fallback 這個 prop 定義要呈現的 JSX 結構就可以了。

```js

import React, { Suspense } from 'react';
import { Route, Switch, Redirect } from 'react-router-dom';

import Layout from './components/layout/Layout';
import LoadingSpinner from './components/ui/LoadingSpinner';

const NewQuote = React.lazy(() => import('./pages/NewQuote'));
const QuoteDetail = React.lazy(() => import('./pages/QuoteDetail'));
const NotFound = React.lazy(() => import('./pages/NotFound'));
const AllQuotes = React.lazy(() => import('./pages/AllQuotes'));

function App() {
  return (
    <Layout>
      <Suspense
        fallback={
          <div className='centered'>
            <LoadingSpinner />
          </div>
        }
      >
        <Switch>
          <Route path='/' exact>
            <Redirect to='/quotes' />
          </Route>
          <Route path='/quotes' exact>
            <AllQuotes />
          </Route>
          <Route path='/quotes/:quoteId'>
            <QuoteDetail />
          </Route>
          <Route path='/new-quote'>
            <NewQuote />
          </Route>
          <Route path='*'>
            <NotFound />
          </Route>
        </Switch>
      </Suspense>
    </Layout>
  );
}

export default App;
```

## 打包

在優化過後，我們還需要經過一個步驟才能發佈到網頁伺服器中，那就是打包我們的專案，因為目前大部分的檔案都是瀏覽器看不懂的檔案或者語法，例如： JSX 或者 Sass 等等。因此我們需要透過 webpack 這類的打包工具，幫我們將這些檔案編譯成瀏覽器看得懂的 HTML、CSS 和 JavaScript。好險若是使用 `react-create-app` 建立專案的話，React 已經幫我們處理好前置步驟了，我們只需要輸入 `npm run build`，就可以完成打包的作業了，是不是很簡單啊！打包完後的檔案都會放到 build 這個資料夾中，而裡面的資料就是我們需要上傳到伺服器的資料。

## 部署到 Firebase

在發佈前，我們需要先到 [Firebase](https://firebase.google.com/) 建立我們的帳號以及我們的 Project。

![Create project](https://i.imgur.com/ghk9uYI.png)

Firebase 提供許多服務，而我們要選擇的是 Hosting 的頁面，進入後我們就可以點擊 Get startd 開始部署的過程。

![Hosting page](https://i.imgur.com/lxGCO7N.png)

第一步，我們需要在 terminal 輸入 `npm install -g firebase-tools` 安裝 Firebase CLI。

![Install Firebase CLI](https://i.imgur.com/ssXKHCZ.png)

安裝完成後，我們需要在 terminal 輸入 `firebase login` 登入 Firebase 帳戶，之後在輸入 `firebase init` 進行 firebase 初始化。這時會看到有一些 Firebase 的功能可以選擇，而因為我們是要 Hosting，所以要選擇的是第三個 `Hosting: Configure files for Firebase Hosting ...`。

![Firebase init](https://i.imgur.com/NX92zGq.png)

接下來，需要選擇這個專案需要存放在 Firebase 的哪個 Project 中，由於我們已經建立好 Project 了，所以選擇 `Use an existing project`，接著選擇預先創立的 Project 就可以了。

![Project Setup](https://i.imgur.com/y0AGFds.png)

之後，會需要知道我們要發佈的檔案路徑，預設是 public，但先前有說過打包後的資料都會存放到 build 這個資料夾中，所以需要輸入的是 build。
![Hosting Setup](https://i.imgur.com/HZUDtFp.png)

下一步會詢問 `Configure as a single-page app (rewrite all urls to /index.html)?` 意思是說，要不要幫我們將所有連結的請求路徑都指向 index.html，會需要這樣做的原因是在 Single Page Application 中，實際上每個網址請求都是由 index.html 這個頁面依照不同的網址渲染出來的，換句話說，就是前端負責控制路由，當輸入網址時，會由前端決定該呈現的畫面是什麼，與傳統上變更網址時，就請求一個不一樣的頁面不同。所以這裡我們要選擇的是 y。這樣我們就不需要自己手動改寫後端路由的規則了，是不是很方便呢？這也是為什麼會選擇 Firebase 的原因。

最後會再詢問是否要自動部署到 GitHub 和是否要覆蓋 public/index.html，都選擇 N 後，就完成了 firebase 的初始化設定了。之後只要輸入 `firebase deploy` 就可以自動部署到 firebase 了！

## 結語

以上就是針對部署 Single Page Application 到 Firebase 的流程介紹。除此之外，市面上還有其他免費的網頁伺服器提供相似的服務，大家也可以試看看，那我們就下次見囉 🤟🏼
