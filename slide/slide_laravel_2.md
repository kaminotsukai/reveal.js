## 連絡先のCRUD実装


>>>


Laravelの処理の流れ

<img src="./test/examples/assets/laravel_image.png" style="width: 800px;">

>>>

```bash
# ヘルプで確認 
$ php artisan help make:model

# apiResourceController / Factory / Migration の作成
$ php artisan make:model Models/Contact --api -fm
```

>>>

<p style="font-size: 20px;">HTMLテンプレートを返却するメソッドを除外したリソースコントローラが作成される</p>
<p style="font-size: 20px;">テストデータ作成するためのfactoryファイル</p>
<p style="font-size: 20px;">マイグレーションファイル</p>

>>>




マイグレーション

<p style="font-size: 20px;">マイグレーションファイルの作成</p>

```bash
[phpコンテナ]$ php artisan make:migration create_contacts_table
Created Migration: xxxx_xx_xx_xxxxxx_create_contacts_table
```

>>>

マイグレーションファイルの編集
<p style="font-size: 20px;">コマンドを実行したら、作成されたファイルを開いてup()の中身を以下のように変更します。</p>

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
<p style="font-size: 20px;">テーブルの作成</p>

```
[phpコンテナ]$ php artisan migrate
```

>>>

モデルの作成
<p style="font-size: 20px; color: green; ">app/Models/Contact/php</p>
<p style="font-size: 20px; color: green; ">Modelsというディレクトリを作成して管理しやすくしています</p>

```php
<?php

namespace App\Models;

use Carbon\Carbon;
// Eloquentを使用します
use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    protected $guarded = ['id'];

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
<p style="font-size: 20px; color: green; ">Factoryを使用してダミーデータを作成していきます</p>

>>>

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

<p style="font-size: 20px; color: green; ">DatabaseSeederに登録することで作成することができます</p>
<p style="font-size: 20px; color: green; ">databases/seeds/DatabaseSeeder.php</p>

```php
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

>>>

```bash
$ php artisan db:seed

```

>>>

ルート定義

<p style="font-size: 20px; color: green; ">以下のように記述してあげることで紐づいたCRUDのアクションにルーティングされるようになります</p>
<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;

// 連絡先API
Route::apiResource('contact', 'ContactController');
```

>>>


```bash
$ php artisan route:list
root@fc294416dd76:/var/www/backend# php artisan route:list
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

<p style="font-size: 20px;">一覧取得API</p>

```php 
public function index()
{
    $contacts = Contact::all();

    return response()->json(['contacts' => $contacts]);
}
```

>>>

<p style="font-size: 20px;">RestClientで確認してみよう</p>



>>>

<p style="font-size: 20px;">登録してみよう</p>
<p style="font-size: 20px;">以下がリクエストに必要なものになります</p>

```php
$table->string('first_name', 20);
$table->string('last_name', 20);
$table->tinyInteger('gender');
$table->string('phone_number');
$table->string('house_phone_number')->nullable();
$table->string('email', 255)->nullable();
$table->string('address')->nullable();
$table->string('birthday')->nullable();
$table->string('memo', 400)->nullable();
```

>>>

```php
public function store(Request $request)
{
    $contact = $request->contact;
    Contact::create($contact);

    return response()->json();
}
```

>>>

<p style="font-size: 20px;">以下のリクエストで確認してみましょう</p>

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


<p style="font-size: 20px">モデル(Eloquentなど)は特定のデータベーステーブルと対応します。</p>
<p style="font-size: 20px">これはデータの取得するのに開発者がSQLを意識しなくてもPHP風に操作することを可能にします</p>
<p style="font-size: 20px; color: green; ">app/Http/Controllers/ContactController.php</p>


>>>

<img src="./test/examples/assets/restclient.png" style="width: 800px;">

>>>

編集機能の実装
<p style="font-size: 20px; color: green; ">idを元にContactを取得してくれる</p>

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

削除

```php
public function destroy(Contact $contact)
{
    $contact->delete();

    return response()->json();
}

```

>>>


destroyかdeleteか
<p style="font-size: 20px">結論、好き嫌い。慣習的にはdestroyは単数削除でdeleteは複数削除</p>


>>>

ちなみにこれまでHTTPステータスコードが200で返却されていた

新規作成などは201で返却してくれるのが理想
<!-- 画像でHTTPステータスコード -->


>>>

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


>>>

リソースの作成

```bash
$ php artisan make:resource ContactResource

```

>>>

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


```php

public function index()
{
    // 1. 
    $contacts = ContactResource::collection(Contact::all());

    return response()->json(['contacts' => $contacts]);
}

public function show(Contact $contact)
{
    // 2. 
    return $contact;
}

```


