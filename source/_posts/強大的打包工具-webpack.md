---
title: 強大的打包工具 - webpack
date: 2022-04-07 16:59:32
tags: [webpack, JavaScript, sass, babel, 模組化, 打包工具]
categories:
- JavaScript
description: Webpack 是一個模組化打包工具，可以將專案中多個 js 檔案編譯成一個壓縮過的 js 檔，而經過一些額外的設定，甚至能夠將png, sass 或者 vue等等非 js 格式的檔案轉譯成瀏覽器能夠閱讀的 css 或者 js 檔案。
---
## 前言

Webpack 是一個模組化打包工具，可以將專案中多個 js 檔案編譯成一個壓縮過的 js 檔，而經過一些額外的設定，甚至能夠將png, sass 或者 vue等等非 js 格式的檔案轉譯成瀏覽器能夠閱讀的 css 或者 js 檔案。而我以前在使用 Vue cli 撰寫專案時，因為預設都會幫你處理好 webpack 部分的基礎設定，只要輸入 `npm run build` 就會幫你自動打包，所以一直沒有深入研究如何客製化 webpack，直到某次在面試時被問到如何處理 webpack 的設定，才讓我驚覺自己根本不懂 webpack，也因此
撰寫這篇文章記錄如何去基礎的客製化 webpack。

![Webpack 的好處](https://i.imgur.com/6qoGE0H.jpg)

## 初始化設定

在專案中可以先在 terminal 輸入 `npm init` 進行 npm 的初始化，之後再輸入 `npm i webpack webpack-cli webpack-dev-server -D` 進行 webpack，webpack-cli 以及 webpack-dev-server 的安裝。會需要額外安裝 webpack-cli 以及 webpack-dev-server 的原因是因為在後續開發上進行打包或者建立伺服器時會使用到。在安裝完成後記得在 package.json 進行指令的設定，名稱可以自訂，不過我習慣用 build 和 serve 來代表打包與建立伺服器。除此之外，也需要在專案中建置 webpack.config.js ，這個檔案主要是用來做一些客製化的設定，切記要將此檔案放置在根目錄不然在後續的編譯上會報錯。以下是針對 devServer 的一些設定。其中 static, compress 和 port 都是預設不太需要改動，而 hot 的作用是每當 dist 內的資源進行變動時，瀏覽器就會自動重新整理。

```js
// package.json
{
  ...
  "scripts": {
    "build": "webpack",
    "serve": "webpack serve"
  },
  ...
}
```

```js
// webpack.config.js
module.exports = {
  devServer: {
    static: {
      directory: path.join(__dirname, 'dist'),
    },
    compress: true,
    port: 9000,
    hot: true,
  },
};
```

## 基本架構

Webpack.config 的主要架構可以分為

- Entry - 主要用來設定檔案的進入點，就像是 vue cli 專案中的 index.js。
- Output - 輸出檔案的位置，預設為一個 dist 的資料夾。
- Loaders - webpack擴充的辨識器，因為 webpack 預設只看得懂 js 檔案，如果想要讓 webpack 能夠識別其他檔案，就必須在這裡做設定。
- Plugins - webpack擴充的套件，像是 html, css檔案編譯等等，都會在這個區域做設定。
- Mode - 開發模式/ 產品模式

在接下來的部分會針對各個區塊做常見設定的介紹。

## Entry

Entry 的設定上相對簡單，就是定義進入點的相對路徑而已，可以設定多個進入點，不過通常只會設定一個。下面是簡易的示範

```markdown
<!-- 目錄結構 -->
project  
│
└─src
│  │
│  └──js
│     │ main.js  
│   
└───node_modules
│
│   webpack.config.js
│   package.json
│   package-lock.json
```

```js
// webpack.config.js
module.exports = {
  entry: './src/js/main.js',
};
```

## Output

Output 在預設的情況下，會在子目錄生成一個 dist 的資料夾，然後在裡面就會有打包過的 js 檔案，而若是另外有使用插件也能夠生成 html 或者 css 檔案，插件的部分這在之後會另外做介紹。以下為簡易的目錄結構與 config 檔的設定。需要取用 path 去取得根目錄的位置，而資料夾名稱可以自訂，至於 filename 就是打包後的 js 要如何命名。這邊可以注意到有加入 [hash] 到檔案名稱中，會這樣設定是用來確保每一次生成的檔名都是獨立的，就像是目錄結構裡的 .js，這樣當用戶在讀取時，就不會因為快取的關係而讀到舊的檔案。

```markdown
<!-- 目錄結構 -->
project
│
└─dist  
│  │ main.f870c5d5c3c52ca5971e.bundle.js   
│
│  
└─src
│  │
│  └──js
│     │ main.js  
│   
└───node_modules
│
│   webpack.config.js
│   package.json
│   package-lock.json
```

```js
// webpack.config.js
const path = require('path');

module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.[hash].bundle.js',
  },
};
```

## Loaders

在前面基本架構有提到，webpack 在預設上是只能讀懂 js 檔案的，因此今天若是要編譯像是 sass, css, vue 甚至是圖片檔，如: png，jpg，就必須在 npm 安裝並在 module 中進行 loader 的設定。以下是我常用的 loader 設定，包含了

- css-loader - 將 css 轉譯成 webpack 看得懂的 js
- postcss-loader - 會需要使用 postcss 的套件 autoprefixer 去做到舊瀏覽器或者跨瀏覽器的樣式支援
- sass-loader - 方便且系統化管理的 css 擴充語法
- babel-loader - 將 js 轉換成支援舊瀏覽器的語法

在安裝時記得 postcss， sass, babel 和 autoprefixer 也要安裝，不要只有安裝 postcss-loader, babel-loader 和 sass-loader。下面是 webpack.config.js, postcss.config.js, babel.config.json 還有 package.json 的設定。其中 webpack 會針對特定檔案，如 postcss.config.js 或者 babel.config.json 去根目錄搜尋，這樣的好處是有關 postcss 或者 babel 相關的設定就不會和其他混在一起。而其中有一個 MiniCssExtractPlugin 是下一個階段會介紹的 plugin，在這裡先暴雷，這主要是用來生成 css 檔用的，因為預設的css會直接寫在 html 裡。需要注意的是預設會是用 style-loader 來處理 css，而這會和 MiniCssExtractPlugin 衝突，因此只能二選一。

```js
// webpack.config.js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/i,
        use: [
          // Creates CSS file
          MiniCssExtractPlugin.loader,
          // Translates CSS into CommonJS
          {
            loader: "css-loader",
            options: {
              sourceMap: true,
            },
          },
          "postcss-loader",
          // Compiles Sass to CSS
          {
            loader: "sass-loader",
            options: {
              // Prefer `dart-sass`
              implementation: require.resolve("sass"),
              sourceMap: true,
            },
          },
        ],
      },
      {
        test: /\.m?js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        }
      }
    ],
  },
};
```

```js
// postcss.config.js
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```

```js
// babel.config.json
{
  "presets": ["@babel/preset-env"]
}
```

主要是用來告知哪些瀏覽器是需要支援的。

```json
// package.json
  "browserslist": [
    "defaults",
    "not ie < 11",
    "last 2 versions",
    "> 1%",
    "iOS 7",
    "last 3 iOS versions"
  ]
```

## Plugin

終於進入到 Plugin 了，webpack 的擴充套件非常多，這裡舉出一些我比較常使用的套件，包含剛剛提到的 MiniCssExtractPlugin 外，還有

- HtmlWebpackPlugin - 生成 html
- CleanWebpackPlugin - 每次打包時會清空 dist 內的資料

一樣都需要使用 npm 安裝這些套件，然後在 webpack.config.js 裡的 plugins 做一些客製化的設定，比如說在 HtmlWebpackPlugin 中就可以定義模板，這樣 webpack 在生成 html 時， body 內才會有資料。而 MiniCssExtractPlugin 中的 [hash] 與前面的 output 一樣都是為了生成唯一名稱的檔案。

```js
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      template: './index.html'
    }),
    new MiniCssExtractPlugin({
      filename: 'main.[hash].css'
    }),
    new CleanWebpackPlugin(),
  ], 
```

## Mode

最後就是 mode 的設定 ~~終於到最後了(撒花)~~ mode 其實也就只是二選一而已，一種是開發者模式，另一種是產品模式。兩者最大的差異在於生成檔案有沒有經過壓縮，通常在使用開發者工具查看某些網站時，有時會發現 css 或者 js 都緊密的排在一起，就像是亂碼一碼，這是因為在產品模式中，為了減少檔案大小，就會將空格的部分全部刪除，才會呈現亂碼的感覺。

```js
// webpack.config.js

module.exports = {
  mode: 'development',
```

## 結語

這次的篇幅比較長，感謝大家耐心看完，而這些都是基本設定而已，webpack 其實還有很多功用我也還沒有使用過，等之後累積到一定的使用經驗，再來分享一些比較進階的使用方法，那我們就下次見囉！
