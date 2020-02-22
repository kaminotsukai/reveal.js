## Laravel + Vue で連絡先

---

## Crowish

<!-- <p style="font-size: 20px">新しいタブで開くことを推奨します</p> -->

---

## 自己紹介


>>>


かみまこと



---

## 目次

>>>

- 作成するミニアプリのデモ
- Dockerで環境構築
- LaravelでAPI開発
- Vue.jsで画面開発

---

## Dockerで環境を構築する

>>>

### ファイル構成

```
sample
    ├── Docker => 各コンテナの設定ファイル
    │   ├── mysql
    │   ├── nginx
    │   │   └── default.conf
    │   ├── vue
    │   │   └── Dockerfile
    │   └── php
    │       ├── Dockerfile
    │       └── php.ini
    ├── var
    │   └── www
    │       ├── backend => Laravel
    │       └── frontend => VueCli
    └── docker-compose.yml
```

>>>

Laravel + Vue + MySQLの環境構築

<p style="font-size: 20px ;color: red;">注意 : ポート番号8080/8000は開けておいてください</p>

```bash
// レポジトリからDockerfileをcloneしてくる
$ git clone https://github.com/kaminotsukai/laraVue-docker-compose.git

// docker-compose.ymlがあるディレクトリまで移動
$ cd laraVue-docker-compose

// dockerコンテナを作成＋起動
$ docker-compose up -d

// コンテナが起動しているか確認(STATUSの項目がUPになっていたら起動完了)
$ docker-compose ps

[省略してます]
STATUS              PORTS                 
Up 52 seconds       0.0.0.0:1234->80/tcp  
Up 52 seconds       0.0.0.0:80->80/tcp    
Up 54 seconds       9000/tcp              
:
```

>>>

今回のDockerの構成

<img src="./test/examples/assets/docker-compose.png" style="width: 800px;">

>>>

## Q&A

>>>

#### Q1. docker-compose のバージョンが違う

<p style="font-size: 20px">A. 以下のコマンドで1.25.3のdocker-composeを取得してください</p>

```bash 
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

>>>

### 確認

>>>

<p style="font-size: 20px">新しいタブで開くことを推奨します</p>

- http://localhost:8000
- http://localhost:1234

---

## LaravelでAPI開発

>>>

## ToDo

>>>

- Laravelの初期設定
- 
- 
- 

>>>

Laravelプロジェクトの作成

```bash 
// phpコンテナのなかに入る
$ docker exec -it php bash 

// laravelプロジェクト作成
[phpコンテナ内]$ laravel new 

// 確認
[phpコンテナ内]$ ls

artisan    composer.json  config    package-lock.json  phpunit.xml  resources  server.php  tests   webpack.mix.js
app	   bootstrap  composer.lock  database  package.json	  public       routes	  storage     vendor
```

>>>

#### DBとの接続

>>>


<p style="font-size: 30px">作成されたプロジェクトはホスト側のvar/backend/にマウントされているので、そのファイルをホスト側のVscodeなどのエディタでいじることができます。</p>

>>>

<p style="font-size: 20px">backend直下にある`.env`というファイルを編集していきます。</p>
<p style="font-size: 20px">ここではDB接続情報などの環境変数を記述していきます。</p>


```.env
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:vzq1t6O3L3bjU79W+oHXDbbdQ19+yAxlfJZhLzR/iDA=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack

<!--ここから上書き  -->
DB_CONNECTION=mysql
DB_HOST=db-host
DB_PORT=3306
DB_DATABASE=database
DB_USERNAME=docker
DB_PASSWORD=docker
<!--ここまで  -->

BROADCAST_DRIVER=log
CACHE_DRIVER=file
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120
```

>>>

<p style="font-size: 20px">phpコンテナの中に入ってDBをマイグレートします。</p>

```bash
$ docker exec -it php bash

[phpコンテナ内]$ php artisan migrate
```


>>>

phpMyAdminで確認

>>>

Laravelの処理の流れ

<img src="./test/examples/assets/laravel_image.png" style="width: 800px;">

>>>


#### ルーティングの作成

>>>

routes/api.php

```php
<?php

use Illuminate\Http\Request;

// Route::middleware('auth:api')->get('/user', function (Request $request) {
//     return $request->user();
// });

Route::post('contacts', 'ContactController@store');


```

>>>

コントローラーの作成

```bash 
$ docker exec -it php bash

[phpコンテナ内]$ php artisan make:controller ContactController -r
```

>>>

処理を書く

app/Http/Controllers/ContactController.php

```php
use App\Models\Contact;

/**
* 連絡先登録API
*
* @param  \Illuminate\Http\Request  $request
* @return \Illuminate\Http\Response
*/
public function store(Request $request)
{
    $contacts = $request->contacts;

    Contact::create($contacts);

    return response()->json();
}
```

>>>


```php
/**
* 連絡先登録API
*
* @param  \Illuminate\Http\Request  $request
* @return \Illuminate\Http\Response
*/
public function store(Request $request)
{
    $contacts = $request->contacts;

    DB::table('contacts')
        ->insert([
            'first_name' => $contacts->first_name,
            'last_name' => $contacts->last_name,
            :
        ]);
}
```

>>>

<p style="font-size: 20px">テーブルを作成するためにマイグレーションファイルを作成して、それに記述していきます。</p>
<p style="font-size: 20px">database/migrations配下に`2020_xx_xx_xxxxxx_create_contacts_table.php`というようなファイルが作成されます。</p>


```bash
$ php artisan make:migration create_contacts_table
```

>>>

<p style="font-size: 20px">作成されたマイグレーションに以下を記述</p>

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateContactsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('contacts', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('first_name', 20);
            $table->string('last_name', 20);
            $table->tinyInteger('gender');
            $table->string('phone_number', 13);
            $table->string('house_phone_number', 13)->nullable();
            $table->string('email', 255)->nullable();
            $table->string('address')->nullable();
            $table->date('birthday')->nullable();
            $table->string('memo', 400)->nullable();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('contacts');
    }
}
```

>>>

<p style="font-size: 20px">作成したマイグレーションファイルをマイグレートする</p>

```bash
$ php artisan migrate
```

>>>

## モデルの作成

>>>

```bash
$ php artisan make:model Models/Contact
```

>>>

app/Models/Contact.php

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    // これを追記
    protected $guarded = ['id'];
}
```

>>>

### APIの挙動を確認する

<p style="font-size: 20px">API開発するなら(Rest Client)</p>
<p style="font-size: 20px">今回は導入が楽なChromeの拡張機能、`Yet Another Rest Client`を使用します。</p>

- [postman](https://www.postman.com/)
- [Yet Another Rest Client](https://chrome.google.com/webstore/detail/yet-another-rest-client/ehafadccdcdedbhcbddihehiodgcddpl)


>>>

## バリデーション

>>>

フォームリクエストの作成

```bash
$ php artisan make:request SaveContactRequest
```

>>>

app/Http/Requests

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class SaveContactRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'contacts' => 'required',
            'contacts.first_name' => 'required|max:20',
            'contacts.last_name' => 'required|max:20',
            'contacts.phone_number' => 'required|numeric',
            'contacts.house_phone_number' => 'nullable|numeric',
            'contacts.email' => 'nullable|email|max:255',
            'contacts.address' => 'nullable|max:100',
            'contacts.birthday' => 'nullable|date',
            'contacts.memo' => 'nullable|date',
            'contacts.gender' => 'required|numeric'
        ];
    }
}

```

>>>

ContactController.php

```php
use App\Http\Requests\SaveContactRequest;

//RequestをSaveContactRequestに変更
public function store(SaveContactRequest $request)
{
    $contacts = $request->contacts;

    Contact::create($contacts);

    return response()->json();
}
```

>>>

## 連絡先取得API開発

>>>

api.php

```php
<?php

use Illuminate\Http\Request;

Route::get('contacts', 'ContactController@index'); // 追加
Route::post('contacts', 'ContactController@store');
```

>>>

contactController.php

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\SaveContactRequest;
use App\Models\Contact;

class ContactController extends Controller
{
    /**
     * 連絡先一覧取得API
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $contacts = Contact::all();

        return response()->json([
            'contacts' => $contacts
        ]);
    }
    :
    :
```

>>>

### Yet Another Rest Client確認

>>>

## 削除API開発

>>>

api.php

```php
<?php

use Illuminate\Http\Request;

Route::get('contacts', 'ContactController@index');
Route::post('contacts', 'ContactController@store');
Route::delete('contacts/{id}', 'ContactController@delete'); // 追記
```

>>>

ContactController.php

```php
/**
* 連絡先削除API
*
* @param  int  $id
* @return \Illuminate\Http\Response
*/
public function destroy($id)
{
    Contact::where('id',$id)->delete();

    return response()->json();
}
```

>>>

## 編集API

>>>

api.php

```php
<?php

use Illuminate\Http\Request;

Route::get('contacts', 'ContactController@index');
Route::post('contacts', 'ContactController@store');
Route::get('contacts/{id}', 'ContactController@edit');
Route::delete('contacts/{id}', 'ContactController@destroy'); // 追記
Route::put('contacts/{id}', 'ContactController@update');　// 追記
```

>>>

ContactController.php

```php
/**
* 編集用連絡先取得API
*
* @param  int  $id
* @return \Illuminate\Http\Response
*/
public function edit($id)
{
    $contacts = Contact::findOrFail($id);

    return response()->json([
        'contacts' => $contacts
    ]);
}

/**
* 連絡先更新API
*
* @param  \Illuminate\Http\Request  $request
* @param  int  $id
* @return \Illuminate\Http\Response
*/
public function update(SaveContactRequest $request, int $id)
{
    $contact = Contact::findOrFail($id);
    $contact->update($request->contacts);

    return response()->json();
}
```

---

## Vueのセットアップ (10)

<p style="font-size: 30px">Vueプロジェクトの作成</p>
<p style="font-size: 30px">axios / vue-router / element uiの導入を行います</p>


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

axiosの導入

```bash
[appコンテナ]$ cd frontend

[appコンテナ]$ npm install axios 

// 立ち上げ
[appコンテナ]$ npm run serve
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

---

## Vue Router導入 (11)
<p style="font-size: 30px">SPAにおける画面遷移を担当するvue-routerを導入していきます。</p>


>>>

インストール

```bash
[appコンテナ]$ npm install vue-router
```

>>>

routerディレクトリの作成
<p style="font-size: 30px; color: green; ">frontend/src/router/index.js</p>

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

<p style="font-size: 30px; color: green; ">frontend/src/main.js</p>

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.use(ElementUI, { size: 'small'})

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')

```


>>>

ルートコンポーネントの作成

<p style="font-size: 30px; color: green; ">frontend/src/App.vue</p>

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

<p style="font-size: 30px; color: green; ">frontend/src/pages/index-contact.vue</p>

```html
<template>
    <div>index-contact.vue</div>
</template>

<script>
export default {};
</script>
```

>>>

<p style="font-size: 30px; color: green; ">frontend/src/pages/save-contact.vue</p>

```html
<template>
    <div>save-contact.vue</div>
</template>

<script>
export default {};
</script>
```

>>>


完成図

---

## 連絡先一覧ページ (12)
<span style="font-size: 30px">今回はElementUIというUIコンポーネントでサクッと実装します</span>

>>>

ElementUIのインストール

```bash
[appコンテナ内]$ npm install element-ui

```

>>>

ElementUIの導入

<p style="font-size: 30px; color: green; ">rontend/src/main.js</p>

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import ElementUI from 'element-ui' // 追記
import 'element-ui/lib/theme-chalk/index.css'　// 追記

Vue.use(ElementUI, { size: 'small'})　// 追記

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
```

>>>

ページの作成
<p style="font-size: 30px; color: green; ">frontend/src/pages/index-contact.vue</p>

```html
<template>
  <el-container 
    style="border: 1px solid #eee; min-height:100vh;"
  >

      <el-header style="text-align: right; font-size: 12px">
        <el-dropdown>
            <i class="el-icon-setting" style="margin-right: 15px"></i>
            <el-dropdown-menu slot="dropdown">
            <el-dropdown-item>View</el-dropdown-item>
            <el-dropdown-item>Add</el-dropdown-item>
            <el-dropdown-item>Delete</el-dropdown-item>
            </el-dropdown-menu>
        </el-dropdown>
      </el-header>
    
    <el-container>
        <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
        <el-menu 
        :default-openeds="['1', '3']"
        :default-active="activeIndex"
        mode="history"
        router
        >
            <el-submenu index="1">
            <template slot="title"><i class="el-icon-message"></i>連絡先登録アプリ</template>
            <el-menu-item-group>
                <template slot="title">基本機能</template>
                <el-menu-item index="1-1">連絡先を登録する</el-menu-item>
            </el-menu-item-group>
            </el-submenu>
        </el-menu>
        </el-aside>
        <el-main>
            <el-table
            :data="contacts" 
            style="width: 100%"
            empty-text="データが存在しません"
            >
            <el-table-column prop="last_name" label="姓" width="140">
            </el-table-column>
            <el-table-column prop="first_name" label="名" width="120">
            </el-table-column>
            <el-table-column prop="gender" label="性別" width="100" :formatter="genderFormatter">
            </el-table-column>
            <el-table-column prop="phone_number" label="電話番号" width="140">
            </el-table-column>
            <el-table-column prop="email" label="Eメール" width="160">
            </el-table-column>
            <el-table-column prop="address" label="住所" width="200">
            </el-table-column>
            <el-table-column prop="birthday" label="誕生日" width="140">
            </el-table-column>
            <el-table-column prop="id" label="操作" width="100">
                <template slot-scope="scope">
                <el-button type="primary" icon="el-icon-edit" circle></el-button>
                <el-button type="danger" icon="el-icon-delete" circle></el-button>
                </template>
            </el-table-column>
            </el-table>
        </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  components: {
    Sidebar,
  },
  data() {
    return {
      contacts: [{
            last_name: '真司',
            first_name: '山田',
            gender: '1',
            phone_number: '080xxxxxxxx',
            email: 'yamada@gmail.com',
            address: '東京都江戸川区',
            birthday: '1990-06-01',
          },{
            last_name: '義一',
            first_name: '斎藤',
            gender: '1',
            phone_number: '080xxxxxxxx',
            email: 'saito@gmail.com',
            address: '東京都江戸川区',
            birthday: '1990-06-01',
          },{
            last_name: '佳代子',
            first_name: '山田',
            gender: '2',
            phone_number: '080xxxxxxxx',
            email: 'kayo@gmail.com',
            address: '東京都江戸川区',
            birthday: '1997-06-01',
          }]
    }
  },
  methods: {
    genderFormatter(row) {
      let gender = ''

      switch(row.gender) {
        case 1:
          gender = '男性'
          break
        case 2:
          gender = '女性'
          break
        default:
          gender = '両方'
          break
      }

      return gender
    },
  }
}
</script>

<style>
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 60px;
  }

  .el-aside {
    color: #333;
  }
</style>
```


---

## 完成

>>>

## 今後したいこと

- jwtなどを使用した認証機能の作成
- stripe決済でECサイトを作る
- 談義(LT的なもの)

>>>

#### ありがとうございました！

>>>

#### 時間が余れば懇親会

