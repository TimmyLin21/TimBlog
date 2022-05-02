---
title: React 學習之旅(15) - React router
date: 2022-05-02 14:50:05
tags: [React, React router, SPA, dynamic route]
categories:
  - React 學習之旅
description: 在大部分的網頁中，我們會利用網址的變化去呈現不同的頁面，但傳統上，每輸入一次網址都必須跟後端發送請求並等待回覆才能將頁面呈現到使用者的畫面，這樣既費時畫面又會有短暫的畫面重整，使用者體驗非常不好。因此後來就有了 React 這樣的框架為我們在前端的部分處理畫面的切換，而前面學到的都是在相同的網址處理畫面，現在就讓我們來瞭解如何使用 React router 幫助我們在切換網址時，不會傳送網頁請求又能切換畫面吧！
---

## 前言

在大部分的網頁中，我們會利用網址的變化去呈現不同的頁面，但傳統上，每輸入一次網址都必須跟後端發送請求並等待回覆才能將頁面呈現到使用者的畫面，這樣既費時畫面又會有短暫的畫面重整，使用者體驗非常不好。因此後來就有了 React 這樣的框架為我們在前端的部分處理畫面的切換，而前面學到的都是在相同的網址處理畫面，現在就讓我們來瞭解如何使用 React router 幫助我們在切換網址時，不會傳送網頁請求又能切換畫面吧！

## React router

### 初始設定

React router 目前主要的版本有 version 6 和 version 5，而這次主要介紹是以 version 5 為主。在安裝上可以在 terminal 輸入 'npm install react-router-dom@5' 就能安裝 version 5 的 React router。在安裝完成後，我們還需要到 index.js 引入 BrowserRoute 並包覆我們的根元件 App 才能使 React router 正常運作。

```js
// index.js
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

import "./index.css";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

### Route

完成上面的初始設定後，在元件中我們就能引入 Route 這個元件，有了它我們就能夠指定在不同的網址下，該呈現什麼樣的元件。如下方的例子中，當網址為 'our-domain.com/welcome' 時，畫面呈現的就是元件 Welcome，而當網址為 'our-domain.com/products' 時，畫面則是呈現元件 Products。通常，Welcome 與 Products 這樣呈現主要畫面的元件，會額外將他們放置到 pages 的資料夾做管理，用於跟一般的元件做區分，但這並不是強制的。

```js
import { Route } from "react-router-dom";
import Welcome from "./pages/Welcome";
import Products from "./pages/Products";

function App() {
  return (
    <main>
      <Route path="/welcome">
        <Welcome />
      </Route>
      <Route path="/products">
        <Products />
      </Route>
    </main>
  );
}

export default App;
```

### Link

在網址切換上，若是直接使用 a 標籤，如: `<a href="/welcome">Welcome</a>`。會發生一個問題，就是我們還是發送了頁面請求，這並不是我們想要的，我們希望能夠在前端解決畫面的切換，因此我們輸要引入另一個元件 Link。在畫面上它依舊是一個 a 標籤，但當使用者點擊時，並不會發送請求，而是將網址變更成 to 這個 props 的值。如下面的示範，當我們點擊 Welcome 或者 Products 時，就能將網址變更成對應的值。

```js
import { Link } from "react-router-dom";

const MainHeader = () => {
  return (
    <header className={classes.header}>
      <nav>
        <ul>
          <li>
            <Link to="/welcome">Welcome</Link>
          </li>
          <li>
            <Link to="/products">Products</Link>
          </li>
        </ul>
      </nav>
    </header>
  );
};
export default MainHeader;
```

### NavLink

但是像是 navbar 上面的連結，使用者通常會希望馬上就能發現目前的頁面是哪一個，所以我們就會針對目前的頁面連結呈現 active 的樣式，而 NavLink 這個元件就能幫助我們輕易的達到這個任務。我們只需要將剛剛的 Link 元件更改成 NavLink 元件，在利用 activeClassName 這個 props 指定 active 時該呈現何種樣式就好了。

```js
import { NavLink } from "react-router-dom";
import classes from "./MainHeader.module.css";

const MainHeader = () => {
  return (
    <header className={classes.header}>
      <nav>
        <ul>
          <li>
            <NavLink activeClassName={classes.active} to="/welcome">
              Welcome
            </NavLink>
          </li>
          <li>
            <NavLink activeClassName={classes.active} to="/products">
              Products
            </NavLink>
          </li>
        </ul>
      </nav>
    </header>
  );
};
export default MainHeader;
```

### Dynamic Route

通常產品列表內都會有許多產品，而每個產品都會有一個自己的詳細產品資訊頁面，但我們並不可能幫每個產品頁面都一一製作 Route 元件，這時我們就必須使用 Dynamic route。我們一樣是使用 Route 來判定畫面該由哪個元件呈現，但是在網址的部分會加上 :anyKey。透過這個 :anyKey 我們就能動態的生成網址，如： 'our-domain.com/product-detail/any-value'。

```js
import { Route } from "react-router-dom";
import Welcome from "./pages/Welcome";
import Products from "./pages/Products";
import ProductDetail from "./pages/ProductDetail";

function App() {
  return (
    <main>
      <Route path="/welcome">
        <Welcome />
      </Route>
      <Route path="/products">
        <Products />
      </Route>
      <Route path="/product-detail/:productId">
        <ProductDetail />
      </Route>
    </main>
  );
}

export default App;
```

有了 dynamic route 我們就能夠給予每一樣產品都有屬於自己的連結，而這個連結在點擊後就會讓畫面呈現 ProductDetail 這個元件。

```js
import { Link } from "react-router-dom";

const Products = () => {
  return (
    <section>
      <h1>The Products Page</h1>
      <ul>
        <li><Link to="/product-detail/book">A book</Link></li>
        <li><Link to="/product-detail/carpet">A carpet</Link></li>
        <li><Link to="/product-detail/wallet">A wallet</Link></li>
      </ul>
    </section>
  )
}

export default Products;
```

但此時每個產品的畫面都是一樣的，而要做出區別我們可以取用網址上的資訊。要如何取得呢？我們就必須到元件中引入 useParams 這個 custom hook。useParams 會回傳一個物件，它的 key 就會是我們剛剛在：後面輸入的 productId，而 value 就會是網址上的值。如：'our-domain.com/product-detail/book' 若是使用 useParams().productId，則得到的值就會是 book。

```js
import { useParams } from "react-router-dom";

const ProductDetail = () => {
  const params = useParams();

  return (
    <section>
      <h1>Product Detail</h1>
      <p>{params.productId}</p>
    </section>
  )
}

export default ProductDetail;
```

### Switch

在 Route 中還有一個比較特別的特性，就是只要是符合的網址都會呈現到畫面中。假如我們把 ProductDetail 呈現的網址變更成 '/products/:productId'，當今天輸入的網址是 'our-domain.com/products/book' 時，Product 和 ProductDetail 都會呈現到畫面上。

```js
import { Route } from "react-router-dom";
import Welcome from "./pages/Welcome";
import Products from "./pages/Products";
import ProductDetail from "./pages/ProductDetail";

function App() {
  return (
    <main>
      <Route path="/welcome">
        <Welcome />
      </Route>
      <Route path="/products">
        <Products />
      </Route>
      <Route path="/products/:productId">
        <ProductDetail />
      </Route>
    </main>
  );
}

export default App;
```

在某些情境下，這或許是我們想要的，但是並不是在這個情境中，因此我們需要引入另一個元件 Switch，它能夠告訴 Route，在畫面上我就只想要顯示最先符合的結果。若是用上面的例子來看的話，呈現的就會是 Product，但這還是不是我們想要的結果，因此我們有兩種做法，第一種是將兩者的位置對調，而另一種則是加入 exact 這個 props。它能夠告訴 Route，只有在網址完全符合時，才呈現元件。經過以下的調整，就能正確的呈現 ProductDetail 囉！

```js
import { Route, Switch } from "react-router-dom";
import Welcome from "./pages/Welcome";
import Products from "./pages/Products";
import ProductDetail from "./pages/ProductDetail";

function App() {
  return (
    <Switch>
      <main>
        <Route path="/welcome">
          <Welcome />
        </Route>
        <Route path="/products" exact>
          <Products />
        </Route>
        <Route path="/products/:productId">
          <ProductDetail />
        </Route>
      </main>
    </Switch>
  );
}

export default App;
```

### Nested Route

Route 並不局限於根元件 App 中，我們也可以將 Route 使用在任何一個 Route 包覆的元件中。但需要注意的是，網址必須是相同路徑延伸下去的。例如下方的例子中，我們在 Welcome 這個元件中又加上一層 Route，我們不能將網址設為 '/products/new-user'，只能設定成 '/welcome/any-value'。

現在當使用者輸入的網址為 'our-domain.com/welcome' 時，畫面只會呈現 'The Welcome Page'，而當輸入的網址改為 'our-domain.com/welcome/new-user' 時，就會跳出額外的 'Welcome, new user!' 了。

```js
import { Route } from "react-router-dom";

const Welcome = () => {
  return (
    <section>
      <h1>The Welcome Page</h1>
      <Route path="/welcome/new-user">
        <p>Welcome, new user!</p>
      </Route>
    </section>
  )
}

export default Welcome;
```

### Redirect

最後為大家介紹 Redirect 這個元件，如同他的名字一樣它能夠幫助我們將網址轉到特定的網址中，例如當使用者輸入 'our-domain.com' 時，我希望網址能轉換到 'our-domain.com/welcome'，這時就可以在 Route 內加上 Redirect 這個元件並搭配 to 這個 prop，這樣 Route 就會自動幫我們轉址到我們要求的網址了，記住 Route 必須加上 exact，因為所有的網址都符合 path="/"，如果不加上 exact 就會陷入無限循環的轉址。

```js
import { Route, Switch, Redirect } from "react-router-dom";
import Welcome from "./pages/Welcome";
import Products from "./pages/Products";
import ProductDetail from "./pages/ProductDetail";

function App() {
  return (
    <Switch>
      <main>
        <Route path="/" exact>
          <Redirect to="/welcome" />
        </Route>
        <Route path="/welcome">
          <Welcome />
        </Route>
        <Route path="/products" exact>
          <Products />
        </Route>
        <Route path="/products/:productId">
          <ProductDetail />
        </Route>
      </main>
    </Switch>
  );
}

export default App;
```

## 結語

以上就是針對 React router 的基本介紹，感謝大家的閱讀，我們下次見囉 🤟🏼
