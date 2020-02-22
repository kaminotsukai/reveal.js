
## Vue Router導入 (6)
<p style="font-size: 30px">SPAにおける画面遷移を担当するvue-routerを導入していきます。</p>

>>>

編集するファイル

```bash
vue
  ├── node_modules
  ├── public
  └──  src
        ├── assets
        ├── components
        ├── pages #新規作成
        │     └── contact
        │            ├── index-contact.vue
        │            └── save-contact.vue
        ├── router #新規作成
        │     └── index.js
        ├── main.js #編集
        └── App.vue #編集

```

>>>

インストール

```bash
[appコンテナ]$ npm install vue-router
```

>>>

routerディレクトリの作成
<p style="font-size: 20px; color: green; ">vue/src/router/index.js</p>

```javascript
import Vue from 'vue';
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const routes = []

const router = new VueRouter({
    mode: 'history',
    routes,
})

export default router
```

>>>

vue-routerを使用可能にする

<p style="font-size: 20px; color: green; ">vue/src/main.js</p>

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')

```


>>>

ルートコンポーネントの作成

<p style="font-size: 20px; color: green; ">vue/src/App.vue</p>

```html
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>
```

>>>

ページコンポーネントの作成
<p style="font-size: 30px">SPAにおける画面遷移を担当するvue-routerを導入していきます。</p>

>>>

<p style="font-size: 30px">src配下にpagesというディレクトリを作成してください</p>

- <span style="font-size: 30px">index-contact.vue(一覧表示)</span>
- <span style="font-size: 30px">save-contact.vue(保存/編集)</span>


>>>

ページの編集

<p style="font-size: 20px; color: green; ">vue/src/pages/contact/index-contact.vue</p>

```html
<template>
    <div>index-contact.vue</div>
</template>

<script>
export default {};
</script>
```

<p style="font-size: 20px; color: green; ">vue/src/pages/contact/save-contact.vue</p>

```html
<template>
    <div>save-contact.vue</div>
</template>

<script>
export default {};
</script>
```

>>>

作成したページをrouterに追加

<p style="font-size: 20px; color: green; ">vue/src/router/index.js</p>

```javascript
import Vue from 'vue';
import VueRouter from 'vue-router'

//　追加
import SaveContact from '../pages/contact/save-contact'
import IndexContact from '../pages/contact/index-contact'

Vue.use(VueRouter)

// 追加
const routes = [
    {
        path: '/',
        name: 'contact',
        component: IndexContact
    },
    {
        path: '/create/contact',
        name: 'create_contact',
        component: SaveContact
    },
]

const router = new VueRouter({
    mode: 'history',
    routes,
})

export default router
```

>>>

確認

- http://localhost:8080/
- http://localhost:8080/create/contact