
## Vueのセットアップ (5)

<p style="font-size: 30px">Vueプロジェクトの作成</p>


>>>

Vue.jsプロジェクトの作成

```bash
// appコンテナに入る
$ docker exec -it app sh

// vueプロジェクト作成
[appコンテナ]$ vue create frontend

? Please pick a preset: default (babel, eslint) 
❯ default
  other

? Pick the package manager to use when installing dependencies: (Use arrow keys)
  Use Yarn 
❯ Use NPM 
```

>>>

frontend/src/main.js

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(ElementUI, { size: 'small'})

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')

```

>>>

Vueが立ち上がっているか確認

http://localhost:8080
