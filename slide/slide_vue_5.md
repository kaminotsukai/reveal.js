
## axios導入 (8)
<p style="font-size: 30px">Laravelで作成したAPIから連絡先のデータを取得しよう</p>

>>>

axiosインストール

```bash
[appコンテナ]$ cd vue

[appコンテナ]$ npm install axios 
```

>>>

一覧ページでデータを取得する
<p style="font-size: 20px; color: green; ">vue/src/pages/contact/index-contact.vue</p>

```javascript
<script>
import axios from 'axios' // 追記
const BASE_URL = 'http://localhost:8000' // 追記

export default {
  data() {
    return {
      contacts: []
    }
  },
  methods: {
    genderFormatter(row) { /* 処理 */ },

    // ここから追記
    axiosGetContacts() {
      const endPoint = BASE_URL + '/api/contacts'
      axios.get(endPoint)
        .then(res => {
          this.contacts = res.data.contacts
        })
        .catch(error => {
            console.warn(error)
        })
    },
    // ここまで
  },
  // ここから追記   
  mounted() {
    this.axiosGetContacts()
  }
　// ここまで
};
</script>
```

>>>

CORSエラーが発生

```
Access to XMLHttpRequest at ‘http://localhost:8000/api/contacts’ 
from origin ‘http://localhost:8080’ has been blocked by CORS
policy: No ‘Access-Control-Allow-Origin’ header is present on the requested resource.
```

>>>

Cors(オリジン間リソース共有)とは
<p style="font-size: 20px;">Webページ上の制限されたリソースを、リソースが提供されたドメイン以外の別のドメインから要求できるようにするメカニズムです</p>
<p style="font-size: 20px;">スキーム、ホスト、ポートがすべて一致した場合のみ、同じオリジンであると見なされます</p>

>>>

Corsミドルウェアの作成

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
    'cors'          => \App\Http\Middleware\Cors::class, // 追加
];
```

>>>

ルート編集
<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;

Route::middleware(['cors'])->group(function () {
    Route::options('{any}');
    Route::get('contacts', 'ContactController@index');
    Route::post('contacts', 'ContactController@store');
    Route::delete('contacts/{id}', 'ContactController@destroy'); 
    Route::get('contacts/{id}', 'ContactController@edit'); 
    Route::put('contacts/{id}', 'ContactController@update');
}
```


