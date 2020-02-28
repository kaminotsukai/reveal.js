
## Vueのセットアップ

<p style="font-size: 30px">Vueプロジェクトの作成</p>


>>>

Vue.jsはJavascriptのフレームワークの一種です

<ul style="font-size: 30px;">
    <li>気軽に使える：Vue.js はjQueryと同様に、scriptタグを1行書くだけで使い始めることができます。</li>
    <li>DOM操作を自動的に行ってくれる</li>
    <li>学習コストが低い。</li>
</ul>

>>>

Vue.jsプロジェクトの作成

```bash
# phpコンテナに入ってる場合は抜ける
[phpコンテナ] exit

# appコンテナに入る
$ docker exec -it app sh

# vueプロジェクト作成
[appコンテナ]$ vue create vue

? Please pick a preset: default (babel, eslint) 
❯ default
  other

? Pick the package manager to use when installing dependencies: (Use arrow keys)
  Use Yarn 
❯ Use NPM 

[appコンテナ]$ cd vue

# 必要なライブラリのインストール
[appコンテナ]$ npm install axios && npm install vue-router && npm install vuetify

# vuetifyが提供するフォントやアイコンなどを使用可能にする
[appコンテナ]$ npm install @mdi/js file-loader material-design-icons-iconfont --save-dev
[appコンテナ]$ npm install @mdi/font -D

# vueを立ち上げる
[appコンテナ]$ npm run serve
```

>>>

Vueが立ち上がっているか確認

<a href="http://localhost:8080" target="_blank">http://localhost:8080</a>


>>>


### Vue Router導入
<p style="font-size: 30px">SPAにおける画面遷移を担当するvue-routerを導入していきます。</p>

>>>

SPAとは
画像
画像

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

routerディレクトリの作成
<p style="font-size: 30px">1. プラグインの使用を宣言する</p>
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

<p style="font-size: 30px">1. プラグインの使用を宣言する</p>
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

<p style="font-size: 30px">２. ルートコンポーネントの作成</p>

>>>

<p style="font-size: 30px">`router-view`にindex.jsで定義したコンポーネントが埋め込まれます</p>
<p style="font-size: 20px; color: green; ">vue/src/App.vue (上書き)</p>

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

<p style="font-size: 30px">3. ページコンポーネントの作成</p>


>>>


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

import SaveContact from '../pages/contact/save-contact' // 追加
import IndexContact from '../pages/contact/index-contact' // 追加

Vue.use(VueRouter)


const routes = [
  // ここから追加
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
  // ここまで
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