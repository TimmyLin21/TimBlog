---
title: React 學習之旅(17) - React router(3)
date: 2022-05-17 20:39:40
tags: [React, React router v6, , Routes, Navigate, Prompt]
categories:
  - React 學習之旅
description: React router 在 2021 年 11 月發佈了第六版，而之前介紹的功能主要都是以第五版為主，所以今天想要分享的是一些常用的功能在第六版是如何變動的，就讓我們一起來瞭解吧！
---
## 前言

React router 在 2021 年 11 月發佈了第六版，而之前介紹的功能主要都是以第五版為主，所以今天想要分享的是一些常用的功能在第六版是如何變動的，就讓我們一起來瞭解吧！

## Routes

在第五版中，會將要呈現的元件包覆在 Route 元件中，但在第六版中，我們會將元件賦予到 element 這個 props 中，整體上我覺得會相對好閱讀許多。除此之外，需要在 Route 的外層再包覆一層 Routes，這個新元件能幫助 Router 更聰明的選擇該呈現的路徑。舉例來說，在第五版裡，假設輸入的網址是 `/teams/new`，`teams/:teamId` 和 `teams/new` 都會符合路徑，所以兩者都會呈現，但在第六版中，由於 `/teams/new` 相較於另一個選擇更為具體，所以 Router 就會聰明的為我們選擇呈現 `/teams/new` 的內容。

```js
import ReactDOM from "react-dom/client";
import {
  BrowserRouter,
  Routes,
  Route,
} from "react-router-dom";
// import your route components too

const root = ReactDOM.createRoot(
  document.getElementById("root")
);
root.render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<App />}>
        <Route index element={<Home />} />
        <Route path="teams" element={<Teams />}>
          <Route path=":teamId" element={<Team />} />
          <Route path="new" element={<NewTeamForm />} />
          <Route index element={<LeagueStandings />} />
        </Route>
      </Route>
    </Routes>
  </BrowserRouter>
);
```

## Navigation

在網址切換的部分，我們還是能夠使用 `Link`，但是 useHistory 就已經被 useNavigate 取代。我們依舊需要將 useNavigate() 賦予到一個變數上，不過不同的是 useNavigate 回傳的是一個 function 而不是物件，所以我們需要給予參數，第一個值可以是前往的網址也可以是數字。`-1` 代表回到上一頁，而 `-2` 則是往前兩頁，以此類推。
而第二個值則是可有可無，如果不想要留下紀錄則可以輸入 `{replace: true}`。

```js
import { useNavigate } from "react-router-dom";

function Invoices() {
  let navigate = useNavigate();
  return (
    <div>
      <NewInvoiceForm
        onSubmit={async (event) => {
          let newInvoice = await createInvoice(
            event.target
          );
          navigate(`/invoices/${newInvoice.id}`, {replace: true});
        }}
      />
    </div>
  );
}
```

除此之外在第五版中的 Redirect 也被移除了，取而代之的是 Navigate。相同地，我們只需要在 Route 中指定使用者輸入哪個網址時 `path=""`，需要轉址到哪個路徑 `to=""` 就可以了。不過記得要加上 `replace={true}`，這樣才不會留下網頁紀錄。

```js
<Route path="/" element={<Navigate to="/welcome" replace={true} />}>
```

## Nested Routes

在第五版中，當要處理 Nested Routes 的時候，我們必須輸入完整的路徑，而面對動態路由時，還需要特別使用 useRouteMatch 去取得目前的路徑，使用起來相當麻煩。但在第六版中，已經不需要這麼辛苦的處理這些事了，因為第六版裡，Nested Routes 使用的是相對路徑，他會自動為我們加上外層的路徑。

```js
function App() {
  return (
    <Routes>
      <Route path="invoices" element={<Invoices />}>
        <Route path=":invoiceId" element={<Invoice />} />
        <Route path="sent" element={<SentInvoices />} />
      </Route>
    </Routes>
  );
}
```

舉例來說，當輸入 `/invoices/123` 時，component tree 就會呈現以下的結構

```js
<App>
  <Invoices>
    <Invoice />
  </Invoices>
</App>
```

而輸入 `/invoices/send` 時，則會呈現以下結構

```js
<App>
  <Invoices>
    <SentInvoices />
  </Invoices>
</App>
```

### Outlet

除此之外，第五版在處理 Nested Routes 時，Route 需要放置到每個元件之中，而當層數越來越多時，我們就必須一一檢視每個元件才能知道整個 Route Tree 的結構。但在第六版中，我們可以使用 Outlet 這個元件來幫助我們將 Route 統一管理。

我們只需要在需要使用 Route 的元件內加上 Outlet，元件就會在輸入相對應的網址時，呈現我們指定的元件到 Outlet 的位置上。這樣我們就可以將 Route 從每一個元件中抽出來，統一在 App 中管理，當我們想要知道 Route Tree 的樣子時，就可以一目瞭然了！

```js
function Invoices() {
  return (
    <div>
      <h1>Invoices</h1>
      <Outlet />
    </div>
  );
}

function App() {
  return (
    <Routes>
      <Route path="invoices" element={<Invoices />}>
        <Route path=":invoiceId" element={<Invoice />} />
        <Route path="sent" element={<SentInvoices />} />
      </Route>
    </Routes>
  );
}
```

## 結語

以上就是針對 v6 版本變動的基本介紹，除了上述的變動外還有一些額外的變動沒有被提到，例如： Prompt 這個元件也被移除了，但或許之後會有新的取代元件。若想知道更多細節，可以參考 React Router 的文件。感謝大家的閱讀，我們下次見囉 🤟🏼
