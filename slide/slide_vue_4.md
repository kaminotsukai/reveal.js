
## 画面開発
<span style="font-size: 30px">今回はVuetifyというUIコンポーネントでサクッと実装します</span>

>>>

一覧画面
<img src="./test/examples/assets/demo1.png" style="width: 800px;">

>>>

新規作成/編集画面
<p style="font-size: 20px;">今回は、登録内容にAWSアカウントを作成していないといけない入力項目があるためバリーデーションは省いております</p>
<img src="./test/examples/assets/demo2.png" style="width: 800px;">


>>>

編集するファイル

```bash
vue
  ├── node_modules
  ├── public
  └──  src
        ├── assets
        ├── components
        ├── pages
        │     └── contact
        │            ├── index-contact.vue #編集
        │            └── save-contact.vue #編集
        ├── router 
        │     └── index.js
        ├── main.js #編集
        └── App.vue 
```

>>>


作成するもの

```bash
・ 一覧画面
・ 新規登録画面
・ [共通] headerコンポーネント
```

>>>

vuetifyの導入
<p style="font-size: 20px; color: green; ">vue/src/main.js</p>

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'

import Vuetify from 'vuetify'
import 'vuetify/dist/vuetify.min.css'
import 'material-design-icons-iconfont/dist/material-design-icons.css'
import '@mdi/font/css/materialdesignicons.css'
Vue.use(Vuetify)

Vue.config.productionTip = false

new Vue({
  vuetify: new Vuetify(),
  router,
  render: h => h(App),
}).$mount('#app')

```



>>>

headerコンポーネントの作成
<p style="font-size: 20px;">headerは様々箇所で使用するため分けて作成します</p>

>>>

<p style="font-size: 20px; color: green; ">vue/src/components/header.vue</p>

```html
<template>
  <v-card
    color="grey lighten-4"
    flat
    tile
    height="100"
  >
    <v-toolbar dense>

      <v-toolbar-title>Contact App</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn text>
        <router-link to="/">連絡先一覧</router-link>
      </v-btn>

      <v-btn text>
        <router-link to="/create/contact">連絡先を登録する</router-link>
      </v-btn>
    </v-toolbar>
  </v-card>
</template>

<script>
export default {
    
}
</script>
```

>>>

一覧画面にヘッダーを導入
<p style="font-size: 20px; color: green; ">vue/src/pages/index-contact.vue</p>

```html
<template>
    <div>
        <Header/>
        <!-- // 中略 -->
    </div>
</template>

<script>
import Header from '../../components/header'

export default {
    components: {
        Header
    }
}
</script>
```

>>>

ヘッダーがついたか確認

>>>

一覧画面なので一覧データが欲しいです。


>>>

APIから連絡先一覧データを取得します

```javascript
import Header from '../../components/header'
import axios from 'axios' // 追記
const BASE_URL = 'http://localhost:8000'

export default {
    components: {
        Header
    },
    data() {
      contacts: []
    },
    // ここから
    methods: {
        axiosGetContacts() {
            let endpoint = BASE_URL + '/api/contact'
            axios.get(endpoint)
                .then(res => {
                    console.log(res)
                })
                .catch(error => {
                    console.warn(error)
                })
        }
    },
    mounted() {
        this.axiosGetContacts()
    }
    // 
    
}
```

>>>

CORSエラーが発生

<img src="./test/examples/assets/cors2.png" style="width: 900px;">

>>>

Cors(オリジン間リソース共有)とは
<p style="font-size: 20px;">Webページ上の制限されたリソースを、リソースが提供されたドメイン以外の別のドメインから要求できるようにする仕組み</p>
<p style="font-size: 20px;">スキーム、ホスト、ポートがすべて一致した場合のみ、同じオリジンであると見なされます</p>
<img src="./test/examples/assets/cors.jpg" style="width: 600px;">

>>>

Corsミドルウェアの作成
<p style="font-size: 20px;">Preflight Request用にレスポンスにアクセスを許容するヘッダーをつける役割を持ちます</p>

```bash
# PHPコンテナの中に入る
$ docker exec -it php bash

# ミドルウェア作成
[phpコンテナ内]$ php artisan make:middleware Cors
```

>>>

Corsミドルウェア編集
<p style="font-size: 20px; color: green; ">app\Http\Middleware\Cors.php</p>

```php
<?php

namespace App\Http\Middleware;

use Closure;

class Cors
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        return $next($request)
            ->header('Access-Control-Allow-Origin', 'http://localhost:8080')
            ->header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS')
            ->header('Access-Control-Allow-Headers', 'Content-Type, X-Requested-With');
    }
}
```

>>>

作成したミドルウェアをKernel.phpに追加
<p style="font-size: 20px; color: green; ">app\Http\Kernel.php</p>

```php
protected $routeMiddleware = [
    'auth'          => \Illuminate\Auth\Middleware\Authenticate::class,
    'auth.basic'    => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'bindings'      => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
    'can'           => \Illuminate\Auth\Middleware\Authorize::class,
    'guest'         => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'signed'        => \Illuminate\Routing\Middleware\ValidateSignature::class,
    'throttle'      => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    'cors'          => \App\Http\Middleware\Cors::class, // 追加
];
```

>>>

ルート編集
<p style="font-size: 20px; ">pliflight requestはoptionsメソッドで送信されるのでoptionsに対して200を返却する</p>
<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;

Route::middleware(['cors'])->group(function () {

    // ここから追記
    Route::options('/{any}', function() {
        return response()->json();
    })->where('any', '.*');
    // ここまで

    Route::get('contacts', 'ContactController@index');
    Route::post('contacts', 'ContactController@store');
    Route::delete('contacts/{id}', 'ContactController@destroy'); 
    Route::get('contacts/{id}', 'ContactController@edit'); 
    Route::put('contacts/{id}', 'ContactController@update');
});
```

>>>

ブラウザでCORSエラーがでないか確認してみる


>>>

取得できたことを確認できたら表示してみる

>>>

<p style="font-size: 20px; color: green; ">index-contact.vue</p>

```html
<template>
  <div>
    <Header/>
    <v-container>
      <v-data-table
          :headers="headers"
          :items="contacts"
          class="elevation-1"
        >
        <template v-slot:item.image="{ item }">
          <v-avatar
            size="36px"
          >
            <img
              v-if="item.avatar != ''"
              :src="item.avatar"
            >
            <v-icon
              v-else
              color="grey"
            ></v-icon>
          </v-avatar>
        </template>
        <template v-slot:item.full_name="{ item }">
          {{ item.full_name }}
        </template>
        <template v-slot:item.phone_number="{ item }">
          {{ item.phone_number }}
        </template>
        <template v-slot:item.email="{ item }">
          {{ item.email }}
        </template>
        <template v-slot:item.gender="{ item }">
          {{ item.gender | genderFilter}}
        </template>
        <template v-slot:item.register_date="{ item }">
          {{ item.created_at }}
        </template>
          <template v-slot:top>
            <v-toolbar flat color="white">
              
              <v-spacer></v-spacer>
            </v-toolbar>
          </template>
          <template v-slot:item.actions="{ item }">
            <v-icon
              small
              class="mr-4"
              @click="editItem(item)"
            >
              edit
            </v-icon>
            
            <v-icon
              small
              @click="deleteItem(item)"
            >
              delete
            </v-icon>
          </template>
        </v-data-table>
      </v-container>
  </div>
</template>

<script>
import Header from '../../components/header'
import axios from 'axios'
const BASE_URL = 'http://localhost:8000'

export default {
    components: {
        Header
    },
    data: () => ({
    contacts: [],
    headers: [
      {
        text: '画像', align: 'start',
        value: 'image', 
      },
      { 
        text: '名前', 
        value: 'full_name', 
      },
      { 
        text: '電話番号(携帯)', 
        value: 'phone_number', 
      },
      { 
        text: 'Eメール', 
        value: 'email', 
      },
      { 
        text: '性別',
        value: 'gender', 
      },
      { 
        text: '誕生日',
        value: 'birthday',
      },
      { 
        text: '操作', 
        value: 'actions',
      },
    ],
  }),
    // ここから
  methods: {
      axiosGetContacts() {
          let endpoint = BASE_URL + '/api/contact'
          axios.get(endpoint)
              .then(res => {
                this.contacts = res.data.contacts
                console.log(res)
              })
              .catch(error => {
                  console.warn(error)
              })
      },
      editItem() {
        // stub
      },
      deleteItem() {
        // stub
      }
    },
    filters: {
      genderFilter(val) {
        let gender = ''

        switch(val) {
          case 1:
            gender = '男性'
            break
          case 2:
            gender = '女性'
            break
          case 3:
            gender = '両方'
            break;
          default:
            gender = ''
            break;
        }

        return gender
      }
    },
    mounted() {
        this.axiosGetContacts()
    }
    // 
}
</script>
```

>>>

削除
<p style="font-size: 20px; color: green; ">index-contact.vue</p>

```javascript
editContact() {
  // stub
},
deleteContact(contact) {
  if (!confirm('削除しますがよろしいでしょうか')) return
  
  this.axiosDeleteContact(contact.id)
},
axiosDeleteContact(id) {
  let endpoint = BASE_URL + `/api/contact/${id}`

  axios.delete(endpoint) 
    .then(res => {
      console.log(res)
      this.refresh()
    })    
    .catch(error => {
      console.log(error)
    })
},
refresh() {
  this.axiosGetContacts()
}
```

>>>

遅いのでローディングバーをつける

<p style="font-size: 20px; color: green; ">components/loading.vue</p>


```html
<template>
    <div>
        <div class="text-xs-center" v-if="loading">
            <v-overlay value="true">
            <v-progress-circular
                :size="50"
                color="primary"
                indeterminate
            ></v-progress-circular>
            </v-overlay>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        loading: Boolean
    }
    
}
</script>
```

>>>

一覧画面にローディングバーを導入
<p style="font-size: 20px; color: green; ">index-contact.vue</p>

```html
<template>
  <div>
    <Header/>
    <v-container>
    <!--  ここから-->
      <Loading
      :loading="loading"
      ></Loading>
      <!-- ここまで -->
      <v-data-table
          :headers="headers"
          :items="contacts"
          class="elevation-1"
        >
      // 中略   
  </div>
</template>

<script>
import Header from '../../components/header'
import Loading from '../../components/loading' //追記
import axios from 'axios'
const BASE_URL = 'http://localhost:8000'

export default {
    components: {
        Header,
        Loading // 追記
    },
    data: () => ({
    contacts: [],
    loading: false, // 追記
    // 中略
  methods: {
        axiosGetContacts() {
            let endpoint = BASE_URL + '/api/contact'
            this.loading = true

            axios.get(endpoint)
                .then(res => {
                  this.contacts = res.data.contacts
                  console.log(res)
                })
                .catch(error => {
                  this.loading = false
                  console.warn(error)
                })
                .finally(() => {
                  this.loading = false
                })
        },
        deleteContact(contact) {
          if (!confirm('削除しますがよろしいでしょうか')) return
          
          this.axiosDeleteContact(contact.id)
        },
        axiosDeleteContact(id) {
          this.loading = true
          let endpoint = BASE_URL + `/api/contact/${id}`

          axios.delete(endpoint) 
            .then(res => {
              console.log(res)
              this.loading = false
              this.refresh()
            })    
            .catch(error => {
              this.loading = false
              console.log(error)
            })
        },
        refresh() {
          this.axiosGetContacts()
        }
    },
    // 中略
}
```

>>>

連絡先登録画面

>>>


<p style="font-size: 20px; color: green; ">save-contact.vue</p>

```html
<template>
    <div>
        <Header/>
        <v-container>
            <v-row>
                <v-col cols="12" sm="6">
                <v-text-field
                    v-model="contact.first_name"
                    label="姓"
                    outlined
                ></v-text-field>
                </v-col>

                <v-col cols="12" sm="6">
                <v-text-field
                    v-model="contact.last_name"
                    label="名"
                    outlined
                ></v-text-field>
                </v-col>
            </v-row>
            <v-radio-group v-model="contact.gender" row>
                <v-radio label="男性" :value="1"></v-radio>
                <v-radio label="女性" :value="2"></v-radio>
                <v-radio label="両方" :value="3"></v-radio>
            </v-radio-group>
            <v-text-field
                v-model="contact.phone_number"
                label="電話番号(携帯)"
                single-line
                outlined
            ></v-text-field>
            <v-text-field
                v-model="contact.house_phone_number"
                label="電話番号(自宅)"
                single-line
                outlined
            ></v-text-field>
            <v-text-field
                v-model="contact.email"
                label="Eメール"
                single-line
                outlined
            ></v-text-field>
            <v-text-field
                v-model="contact.address"
                outlined
                label="住所"
            ></v-text-field>
            <v-text-field
                v-model="contact.birthday"
                label="誕生日"
                single-line
                outlined
            ></v-text-field>
            <v-textarea
                v-model="contact.memo"
                label="メモ"
                auto-grow
                outlined
                rows="3"
                row-height="25"
            ></v-textarea>
            <v-file-input accept="image/*" label="avatar"></v-file-input>           
            <div class="my-5">
                <v-btn large>登録</v-btn>
            </div>
        </v-container>
    </div>

</template>

<script>
import Header from '../../components/header'

export default {
    components: {
        Header
    },
    data() {
        return {
            contact: {
              'first_name': '',
              'last_name': '',
              'avatar': '',
              'gender': '',
              'phone_number': '',
              'house_phone_number': '',
              'email': '',
              'address': '',
              'birthday': '',
              'memo': ''
            },
        }
    }
    
}
</script>

```

>>>

登録画面にローディングバーをつける
<p style="font-size: 20px; color: green; ">save-contact.vue</p>

```html
<template>
    <div>
        <Header/>
        <v-container>
        <!--  ローディングバー追加-->
            <Loading
            :loading="loading"
            ></Loading>
        <!--  -->
            <v-row>
        <!-- 中略 -->
            <v-file-input accept="image/*" label="avatar"></v-file-input>           
            <div class="my-5">
            <!--saveContact追加  -->
                <v-btn large @click="saveContact()">登録</v-btn>
            </div>
        </v-container>
    </div>

</template>

<script>
import Header from '../../components/header'
// axios とローディングバー追記
import Loading from '../../components/loading'
import axios from 'axios'

const BASE_URL = 'http://localhost:8000'

export default {
    components: {
        Header,
        Loading // 追記
    },
    data() {
        return {
            loading: false,　// 追記
            contact: {
              'first_name': '',
              'last_name': '',
              'avatar': '',
              'gender': '',
              'phone_number': '',
              'house_phone_number': '',
              'email': '',
              'address': '',
              'birthday': '',
              'memo': ''
            },
        }
    },
    methods: {
        // 保存処理
        saveContact() {
            if (!confirm('この内容で登録してもよろしいでしょうか')) return 
            this.axiosSaveContact()
        },
        axiosSaveContact() {
            this.Loading = true
            let endpoint = BASE_URL + '/api/contact'
            const param = {
                'contact': this.contact
            }
            axios.post(endpoint, param)
                .then(res => {
                    console.log(res)
                    this.$router.push({name: 'contacts'})
                })
                .catch(error => {
                    this.Loading = false
                    console.warn(error)
                })
                .finally(() => {
                    this.Loading = false
                })
        }
    }
    
}
</script>
```

>>>

実際に登録してみよう

>>>

<p style="font-size: 30px;">現状、登録したものが一覧の最後尾にきてしまうので最新のものから取得するようにしましょう。</p>
<p style="font-size: 20px; color: green; ">app/Http/Controller/ContactController.php</p>

```php
public function index()
{
  // all() -> latest
    $contacts = ContactResource::collection(Contact::latest()->get());

    return response()->json(['contacts' => $contacts]);
}
```

>>>

編集画面開発

>>>

<p style="font-size: 30px;">保存画面と同じ画面を使用するため同じコンポーネントを指定します</p>
<p style="font-size: 20px; color: green; ">router/index.js</p>

```javascript
const routes = [
    {
        path: '/',
        name: 'contacts',
        component: IndexContact
    },
    {
        path: '/create/contact',
        name: 'create_contact',
        component: SaveContact
    },    
    {
        path: '/edit/contact/:id',
        name: 'edit_contact',
        component: SaveContact
    },
]
```


>>>

<p style="font-size: 20px; color: green; ">index-contact.vue</p>

```javascript
// 編集画面への遷移処理を追加します
editContact(contact) {
  let id = contact.id
  this.$router.push({name: 'edit_contact', params: { id: id }})
},
deleteContact(contact) {
  if (!confirm('削除しますがよろしいでしょうか')) return
  
  this.axiosDeleteContact(contact.id)
},
```


>>>

このままだと、異なるURLで新規保存をするだけになります。

なので編集ということを判別させていきます

>>>

編集画面か保存画面かどうかを判定する
<p style="font-size: 20px;">URLから判定します</p>
<p style="font-size: 20px; color: green; ">save-contact.vue</p>

```javascript
    computed: {
        newContact() {
            const routeParam = this.$route.params.id + ''
            return !routeParam.match(/^\d+$/)
        },
        saveContactTitle() {
            return this.newContact ? '登録' : '更新'
        },
        id () {
            return this.$route.params.id
        }
    },
    methods: {
        saveContact() {
            if (!confirm('この内容で登録してもよろしいでしょうか')) return 
            this.axiosSaveContact()
        },
        axiosSaveContact() {
            this.Loading = true
            let endpoint = BASE_URL + '/api/contact'
            const param = {
                'contact': this.contact
            }
            axios.post(endpoint, param)
                .then(res => {
                    console.log(res)
                    this.$router.push({name: 'contacts'})
                })
                .catch(error => {
                    this.Loading = false
                    console.warn(error)
                })
                .finally(() => {
                    this.Loading = false
                })
        },
        axiosGetContact(id) {
          this.loading = true
          const endPoint = BASE_URL + `/api/contact/${id}`
          
          axios.get(endPoint)
            .then(res => {
                console.log(res)
                this.contact = res.data.data
            })
            .catch(error => {
                this.loading = false
                console.warn(error)
            })
            .finally(() => {
                this.loading = false
            })
        }
    },
    created() {
      if (!this.newContact) {
          this.axiosGetContact(this.id)
    }
  } 
    
```


>>>


データの更新処理


```javascript
  computed: {
        newContact() {
            const routeParam = this.$route.params.id + ''
            return !routeParam.match(/^\d+$/)
        },
        saveContactTitle() {
            return this.newContact ? '登録' : '更新'
        },
        id () {
            return this.$route.params.id
        }
    },
    methods: {
      // 更新か新規作成か判別
        saveContact() {
            if (!confirm(`この内容で${this.saveContactTitle}してもよろしいでしょうか`)) return 
            this.loading = true
            const id = this.id

            if (this.newContact) {
                this.axiosSaveContact()
            } else {
                this.axiosUpdateContact(id)
            }
        },
        // 更新用の処理
        axiosUpdateContact(id) {
            const endPoint = BASE_URL + `/api/contact/${id}`
            const param = {
                'contact': this.contact
            }

            axios.put(endPoint, param)
                .then(res => {
                    console.log(res)
                    this.$router.push({name: 'contacts'})
                })
                .catch(error => {
                    this.loading = false
                    console.warn(error)
                })
                .finally(() => {
                    this.loading = false
                })
        },
    },
```