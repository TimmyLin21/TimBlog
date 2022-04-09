---
title: Vue 生態鏈的狀態管理工具 - Vuex
date: 2022-04-09 13:54:05
tags: [Vuex, Vue, 跨元件傳遞, 狀態管理]
categories:
- Vue
description: Vuex 是 Vue 生態鏈中的狀態管理工具，可以用來處理跨元件資料傳遞的問題，相較於 Vue 2 的 Eventbus 和 Vue 3 的 mitt.js，Vuex 因為單一資訊來源的特性，使我們在資料管理上更加方便，不需要在多個元件上重複進行變更。除此之外，它還有單向資料流的特性，讓我們在除錯上也比較容易追蹤錯誤來源。而說了這麼多 Vuex 的優點，就讓我們來瞭解如何使用 Vuex 吧！
---
## 前言

Vuex 是 Vue 生態鏈中的狀態管理工具，可以用來處理跨元件資料傳遞的問題，相較於 Vue 2 的 Eventbus 和 Vue 3 的 mitt.js，Vuex 因為單一資訊來源的特性，使我們在資料管理上更加方便，不需要在多個元件上重複進行變更。除此之外，它還有單向資料流的特性，讓我們在除錯上也比較容易追蹤錯誤來源。而說了這麼多 Vuex 的優點，就讓我們來瞭解如何使用 Vuex 吧！

## 基本結構

如果是使用 Vue Cli 安裝 Vuex，應該可以發現一個 store 的資料夾，而裡面有一個 index.js 的檔案，而初始狀態應該是這樣

```js
import { createStore } from 'vuex';

export default createStore({
  state: {},
  getters: {},
  mutations: {},
  actions: {},
})
```

以資料流的方式去解釋各個 api 的功用，就會像是 Vuex 有一個 state，它的作用就類似於資料庫，當元件在 created 的生命週期時，可以發送請求到 Vuex 的 actions，這時 actions 就會像後端請求資料，再透過 mutations 更改 state 內的資料。而更改後的資料又可以渲染進元件內。除此之外，還有一個 api 是 getters，其作用就像是 computed，在後續的介紹中會再一一說明各個 api 的使用方法。

![Vuex 結構圖](https://vuex.vuejs.org/vuex.png)

## State

前面我們有提到 state 就類似於資料庫，而身為一個資料庫，我們要如何從裡面拿到資料呢？這時我們就必需在需要資料的元件內使用 computed 的方式去取得資料。

```js
// index.js
state: {
  name: 'Tim' 
}
```

```js
// component
computed: {
  name() {
    return this.$store.state.name;
  }
};
```

而當資料超過一筆以上時，我們也可以使用 Vuex 提供的 mapState 函式做多筆資料的傳入，就不必輸入一長串的程式碼了。

```js
// index.js
state: {
  firstName: 'Tim',
  lastName: 'Lin' ,
}
```

```js
// component
import { mapState } from 'vuex';

export default {
  computed: mapState([
    'firstName',
    'lastName',
  ]);
}
```

## Getters

當我們今天想要將資料處理過後再輸出時，就可以使用 getters，其作用就類似於 computed，使用上也類似於 computed。

```js
// index.js
state: {
  firstName: 'Tim',
  lastName: 'Lin',
},
getters: {
  name(state) {
    return `${state.firstName} ${state.lastName}` 
  }
},
```

```js
// component
computed: {
  name() {
    return this.$store.getters.name;
  }
};
```

而 Vuex 也為 getters 提供能夠一次傳入多個值得函式 mapGetters，使用方法與 mapState 一樣，記得元件要 import mapGetters 才能使用。

## Mutations

在一開始我們有提到 Vuex 具有單向資料流的特性，也就是說所有資料都必須經過一個特定的途徑才能修改。而這個途徑就是提交 (commit) 某個 mutations 的屬性。在以下的例子中，我們先在 mutations 中定義一個 setFirstName 的 callback function，而其參數分別是 state 以及傳入的值。接著我們在元件內建立一個 input ，當資料變動時，就會提交 setFirstName 到 Vuex。當 Vuex 接收到 setFirstName 就會取用傳入的參數並更新 state 內的值。概念就很像是 emit 在父層綁定監聽事件的感覺。雖然使用 v-model，但由於單向資料流的特性，資料不能直接更動，所以才會需要使用 get() 和 set() 達到響應式。

```html
<template>
  <input type="text" v-model="firstName">
  {{ name }}
</template>

<script>
export default {
  computed: {
    firstName: {
      get() {
        return this.$store.state.firstName;
      },
      set(val) {
        this.$store.commit('setFirstName', val);
      },
    },
    name() {
      return this.$store.getters.name;
    },
  },
};
</script>
```

```js
import { createStore } from 'vuex';

export default createStore({
  state: {
    firstName: 'Tim',
    lastName: 'Lin',
  },
  getters: {
    name(state) {
      return `${state.firstName} ${state.lastName}`;
    },
  },
  mutations: {
    setFirstName(state, payload) {
      state.firstName = payload;
    },
  },
});
```

Vuex 一樣有提供 mapMutations 的函式大幅簡化了原有的寫法

```js
import { mapMutations } from 'vuex';

export default {
  computed: {
    firstName: {
      get() {
        return this.$store.state.firstName;
      },
      set(val) {
        this.setFirstName(val);
      },
    },
    name() {
      return this.$store.getters.name;
    },
  },
  methods: {
    ...mapMutations(['setFirstName']),
  },
};
```

## Actions

剛剛所敘述的 mutations 只能處理同步的資料修改，對於非同步的任務，如 async/await 或者 promise，就必須使用 actions。以下的例子中，我們一樣在 actions 先定義一個 getRandomUser 的 callback funciton，然後在元件內給予其 created 的生命週期時派發 (dispatch) getRandomUser 到 Vuex，概念與剛剛的 setFirstName 一樣，只是指令不同而已。而這時 Vuex 就會進行非同步的任務，當任務完成時再使用 mutations 中的 setFirstName 變更 state 中的 firstName。最後經過 getters 回傳到元件內。

```html
<template>
  {{ name }}
</template>

<script>

export default {
  computed: {
    name() {
      return this.$store.getters.name;
    },
  },
  created() {
    this.$store.dispatch('getRandomUser');
  },
};
</script>
```

```js
import { createStore } from 'vuex';
import axios from 'axios';

export default createStore({
  state: {
    firstName: 'Tim',
    lastName: 'Lin',
  },
  getters: {
    name(state) {
      return `${state.firstName} ${state.lastName}`;
    },
  },
  mutations: {
    setFirstName(state, payload) {
      state.firstName = payload;
    },
  },
  actions: {
    getRandomUser(context) {
      axios.get('https://randomuser.me/api/')
        .then((res) => {
          context.commit('setFirstName', res.data.results[0].name.first);
        });
    },
  },
});
```

而 mapActions 的使用方法與剛剛的 mapMutations 一樣，故不多做介紹。

## 結語

以上就是針對 Vuex 以及它所提供 api 的使用方法介紹，過一陣子會再來研究看看 Pinia 的使用方法，之後再與大家分享，那就下次見囉！
