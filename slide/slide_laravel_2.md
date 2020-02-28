## 連絡先のCRUD実装


>>>

CRUDとは
<p style="font-size: 20px;">データ永続性のための基本機能の名称</p>

- create (新規作成)
- read (読み取り)
- update (更新)
- delete (削除)


>>>


Laravelの処理の流れ

<img src="./test/examples/assets/laravel_image.png" style="width: 800px;">

>>>

CRUDに必要な物を先に作成してしまいます

```bash
# ヘルプで確認 
$ php artisan help make:model

# apiResourceController / Factory / Migration の作成
$ php artisan make:model Models/Contact --api -fm
```

>>>

<ul style="font-size: 30px;">
    <li>ApiController: HTMLテンプレートを返却するメソッドを除外したリソースコントローラが作成される</li>
    <li>factory: テストデータ作成するためのファイル</li>
    <li>migration: DBテーブルの設計図</li>
</ul>

>>>

マイグレーション
<p style="font-size: 20px;">テーブル設計図のようなもので、このファイルをマイグレートすることで接続してるDBに記述したテーブルが作成されます
</p>


>>>

マイグレーションファイルの編集
<p style="font-size: 20px;">作成されたマイグレーションファイルを開いてup()の中身を以下のように変更します。</p>

<p style="font-size: 20px; color: green; ">database/migrations/xxxx_xx_xx_xxxxxx_create_contacts_table.php</p>

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateContactsTable extends Migration
{
    public function up()
    {
        Schema::create('contacts', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('first_name', 20);
            $table->string('last_name', 20);
            $table->string('avatar')->nullable();
            $table->tinyInteger('gender');
            $table->string('phone_number');
            $table->string('house_phone_number')->nullable();
            $table->string('email', 255)->nullable();
            $table->string('address')->nullable();
            $table->string('birthday')->nullable();
            $table->string('memo', 400)->nullable();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('contacts');
    }
}

```

>>>

マイグレーション
<p style="font-size: 20px;">マイグレーションファイルを元にテーブルを作成</p>

```
[phpコンテナ]$ php artisan migrate
```

>>>

モデルの作成
<p style="font-size: 20px;">モデルはMVCアーキテクチャにおいてDBとのやりとりを担当しています</p>

>>>

<p style="font-size: 20px; color: green; ">app/Models/Contact/php</p>

```php
<?php

namespace App\Models;

use Carbon\Carbon;
// Eloquentを使用します
use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    protected $guarded = ['id'];

    // テーブルにはない独自の属性を追加することもできます
    public function getPathAttribute(): string
    {
        return asset("/contact/$this->id");
    }

    public function getFullNameAttribute(): string
    {
        return $this->last_name.' '.$this->first_name;
    }
}

```


>>>


テストデータの作成
<p style="font-size: 20px;">Factoryを使用してダミーデータを作成していきます</p>
<p style="font-size: 20px;">ダミーデータを作成することでテスト(確認がしやすくなります)</p>

>>>

<p style="font-size: 20px;">$fakerというダミーデータを提供してくれるメソッドを使用して作成していきます</p>
<p style="font-size: 20px;">もちろん、関数も記述可能です</p>
<p style="font-size: 20px; color: green; ">databases/factories/ContactFactory.php</p>

```php
$factory->define(Contact::class, function (Faker $faker) {
    return [
        'first_name' => $faker->firstName,
        'last_name' => $faker->lastName,
        'avatar' => 'https://avatars0.githubusercontent.com/u/9064066?v=4&s=460',
        'gender' => function() {
            return mt_rand(1,3);
        },
        'phone_number' => $faker->phoneNumber,
        'house_phone_number' => '',
        'email' => $faker->email,
        'address' => $faker->streetAddress,
        'birthday' => $faker->date($format = 'Y-m-d'),
        'memo' => $faker->text,
    ];
});

```


>>>

<p style="font-size: 20px;">DatabaseSeederに登録することで特定のコマンド時に実行され作成することができます</p>
<p style="font-size: 20px; color: green; ">databases/seeds/DatabaseSeeder.php</p>

```php
use App\Models\Contact;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     *
     * @return void
     */
    public function run()
    {
        // $this->call(UsersTableSeeder::class);
        factory(Contact::class, 30)->create();
    }
}

```

<p style="font-size: 20px;">DatabaseSeederに登録されているファイルを実行するコマンド</p>

```bash
[phpコンテナ内] $ php artisan db:seed
```

>>>

phpMyAdminで登録されているか確認する

http://localhost:1234


>>>

ルート定義
<p style="font-size: 20px;">ここまででデータ周りのタスクは完了しました</p>
<p style="font-size: 20px;">リクエストを受けて、適切な処理に渡す担当です</p>


>>>

<p style="font-size: 20px;">以下のように記述してあげることで紐づいたCRUDのアクションにルーティングされるようになります</p>
<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;
use App\Http\Resources\ContactResource;

// 連絡先API
Route::apiResource('contact', 'ContactController');
```

>>>

<p style="font-size: 20px;">以下のコマンドを叩くと現在登録されているルーティングを確認することができます</p>
<p style="font-size: 20px;">つまり、`GET .../api/contact`にアクセスするとContactコントローラーのindexメソッドにリクエストが渡されるようになります</p>

```bash
[phpコンテナ内]$ php artisan route:list

+--------+-----------+-----------------------+-----------------+------------------------------------------------+--------------+
| Domain | Method    | URI                   | Name            | Action                                         | Middleware   |
+--------+-----------+-----------------------+-----------------+------------------------------------------------+--------------+
|        | GET|HEAD  | /                     |                 | Closure                                        | web          |
|        | GET|HEAD  | api/contact           | contact.index   | App\Http\Controllers\ContactController@index   | api          |
|        | POST      | api/contact           | contact.store   | App\Http\Controllers\ContactController@store   | api          |
|        | GET|HEAD  | api/contact/{contact} | contact.show    | App\Http\Controllers\ContactController@show    | api          |
|        | PUT|PATCH | api/contact/{contact} | contact.update  | App\Http\Controllers\ContactController@update  | api          |
|        | DELETE    | api/contact/{contact} | contact.destroy | App\Http\Controllers\ContactController@destroy | api          |
|        | GET|HEAD  | api/user              |                 | Closure                                        | api,auth:api |
+--------+-----------+-----------------------+-----------------+------------------------------------------------+--------------+

```

>>>

一覧取得APIの実装
<p style="font-size: 20px;">一覧取得はContactControllerのindexメソッドで処理します</p>

>>>

<p style="font-size: 20px;">一覧取得API</p>

```php 
public function index()
{
    $contacts = Contact::all();

    return response()->json(['contacts' => $contacts]);
}
```

>>>

処理のイメージ
<img src="./test/examples/assets/model.png" style="width: 800px;">

>>>

RestClientで確認してみよう
<p style="font-size: 20px;">APIのテストをする際によく使用されるツールです。</p>

<ul style="font-size: 30px;">
    <li>postman</li>
    <li>yet another rest client (chromeの拡張機能)</li>
</ul>

>>>

確認してみる
<p style="font-size: 20px;">statusが200/データが返却されると正常に動いています</p>
<img src="./test/examples/assets/rest_get.png" style="width: 800px;">


>>>

残りのAPI開発
<p style="font-size: 20px;">新規作成/編集/削除を作成していきます</p>

>>>

<p style="font-size: 20px; color: green; ">app/Http/Controller/ContactController.php</p>

```php
// 中略
public function store(Request $request)
{
    $contact = $request->contact;
    Contact::create($contact);

    return response()->json();
}
```

>>>

<p style="font-size: 20px;">以下のリクエストをrest clientで確認してみましょう</p>

<p style="font-size: 20px;">payloadという箇所にこれを入力してください</p>

```
<!-- payload -->

{
    "contact": {
    "first_name": "山田",
    "last_name": "太郎",
    "phone_number": "08012127171",
    "house_phone_number": "",
    "email": "taro@gmail.com",
    "address": "大阪府岸和田市",
    "birthday": "1990-01-01",
    "memo": "",
    "gender": 1
    }
}
```

>>>


フォームリクエスト
<p style="font-size: 20px;">リクエストのバリデーション担当者</p>
<p style="font-size: 20px;">コントローラーに直接記述することも可能ですが、煩雑化を防ぐために責務を分けて記述しています</p>

```bash
[phpコンテナ内]$ php artisan make:request SaveContactRequest
```

>>>

フォームリクエストの編集
<p style="font-size: 20px; color: green; ">app/Http/Requests/SaveContactRequest.php</p>

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class SaveContactRequest extends FormRequest
{
    /**
     * 認証機能がある場合に使用します
     *
     * @return bool
     */
    public function authorize()
    {
        return true; // false -> true
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'contact' => 'required',
            'contact.first_name' => 'required|max:20',
            'contact.last_name' => 'required|max:20',
            'contact.phone_number' => 'required',
            'contact.house_phone_number' => 'nullable',
            'contact.email' => 'nullable|email|max:255',
            'contact.address' => 'nullable|max:100',
            'contact.birthday' => 'nullable|date',
            'contact.memo' => 'nullable',
            'contact.gender' => 'required|numeric'
        ];
    }
}

```

>>>

フォームリクエストの導入

<p style="font-size: 20px; color: green; ">app/Http/Controller/ContactController.php</p>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\SaveContactRequest;
use App\Models\Contact;
use Illuminate\Http\Request;

class ContactController extends Controller
{
    // 中略

    // 1. save -> SaveContactRequest
    public function store(SaveContactRequest $request)
    {
        $contact = $request->contact;
        Contact::create($contact);

        return response()->json();
    }

    // 2. save -> SaveContactRequest
    public function update(SaveContactRequest $request, Contact $contact)
    {
        //
    }

    // 中略
}

```

>>>

確認してみましょう
<p style="font-size: 20px;">422エラーが出ると成功です</p>

```bash
{
    "contact": {
    "first_name": "",  # requiredですが、空にしてみます
    "last_name": "太郎",
    "phone_number": "08012127171",
    "house_phone_number": "",
    "email": "taro@gmail.com",
    "address": "大阪府岸和田市",
    "birthday": "1990-01-01",
    "memo": "",
    "gender": 1
    }
}
```

>>>

<img src="./test/examples/assets/restclient.png" style="width: 800px;">

>>>

編集機能
<p style="font-size: 20px;">idを元にContactを取得してくれる</p>

```php
// 編集用データの取得
public function show(Contact $contact)
{
    return $contact;
}

// 更新
public function update(SaveContactRequest $request, Contact $contact)
{
    $contact->update($request->contact);

    return response()->json();
}
```


>>>

削除機能

```php
public function destroy(Contact $contact)
{
    $contact->delete();

    return response()->json();
}

```

>>>

リソースを作成する
<p style="font-size: 20px">リソースとはレスポンスのデータをいい感じに整形してくれる役割を持っています</p>

>>>


リソースの作成

```bash
$ php artisan make:resource ContactResource

```

>>>

<p style="font-size: 20px; color: green;">app/Http/Resources/ContactResource.php</p>

```php
public function toArray($request)
{
    // return parent::toArray($request);
    return [
        'id' => $this->id,
        'first_name' => $this->first_name,
        'last_name' => $this->last_name,
        'full_name' => $this->full_name,
        'avatar' => $this->avatar,
        'gender' => $this->gender,
        'phone_number' => $this->phone_number,
        'house_phone_number' => $this->house_phone_number,
        'email' => $this->email,
        'address' => $this->address,
        'birthday' => $this->birthday,
        'memo' => $this->memo,
        'path' => $this->path,
        'created_at' => $this->created_at->diffForHumans()
    ];
}

```

>>>

コントローラーに導入します
<p style="font-size: 20px; color: green;">app/Http/Controller/ContactController.php</p>

```php

public function index()
{
    // 1. 書き換える
    $contacts = ContactResource::collection(Contact::all());

    return response()->json(['contacts' => $contacts]);
}
```

>>>

これでRestClientで確認してみてください
<p style="font-size: 20px">さっきとは違うレスポンスが帰ってきてると思います</p>

>>>

これでAPI開発はひと段落しました
<p style="font-size: 20px">以降は豆知識要素があります</p>

>>>


destroyかdeleteか
<p style="font-size: 20px">結論、好き嫌い。慣習的にはdestroyは単数削除でdeleteは複数削除</p>


>>>

<p style="font-size: 35px">現在、全てのAPIがステータスコード200で返却されています</p>
<p style="font-size: 30px">新規作成などは201で返却してくれるのが理想</p>


[ステータスコード](https://developer.mozilla.org/ja/docs/Web/HTTP/Status)


>>>

適切なステータスコードを返却する

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\SaveContactRequest;
use App\Models\Contact;
use Illuminate\Http\Request;

// このファイルにステータスコードが定義されている
use Symfony\Component\HttpFoundation\Response;

class ContactController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $contacts = Contact::all();

        return response()->json(['contacts' => $contacts]);
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(SaveContactRequest $request)
    {
        $contact = $request->contact;
        Contact::create($contact);

        // 1. 201返却
        return response()->json([], Response::HTTP_CREATED);
    }

    /**
     * Display the specified resource.
     *
     * @param  \App\Models\Contact  $contact
     * @return \Illuminate\Http\Response
     */
    public function show(Contact $contact)
    {
        return $contact;
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \App\Models\Contact  $contact
     * @return \Illuminate\Http\Response
     */
    public function update(SaveContactRequest $request, Contact $contact)
    {
        $contact->update($request->contact);

        // 2. 200
        return response()->json([], Response::HTTP_ACCEPTED);
    }

    /**
     * Remove the specified resource from storage.
     *
     * @param  \App\Models\Contact  $contact
     * @return \Illuminate\Http\Response
     */
    public function destroy(Contact $contact)
    {
        $contact->delete();

        // 3. 204
        return response()->json([], Response::HTTP_NO_CONTENT);
    }
}

```


