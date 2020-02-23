## 連絡先登録API (3)

>>>

request

```
contacts: {
    first_name,
    last_name,
    gender,
    phone_number,
    house_phone_number,
    email,
    address,
    birthday,
    memo
}
```

>>>


Laravelの処理の流れ

<img src="./test/examples/assets/laravel_image.png" style="width: 800px;">

>>>


マイグレーション

<p style="font-size: 20px;">マイグレーションファイルの作成</p>

```bash
[phpコンテナ]$ php artisan make:migration create_contacts_table
Created Migration: xxxx_xx_xx_xxxxxx_create_contacts_table
```

>>>

マイグレーション
<p style="font-size: 20px;">マイグレーションファイルの編集</p>

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
<p style="font-size: 20px;">連絡先モデルの作成</p>

```bash
[phpコンテナ]$ php artisan make:model Models/Contact
```

>>>

モデルの作成
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

    public function setBirthdayAttribute($birthday): void
    {
        $this->attributes['birthday'] = Carbon::parse($birthday);
    }
}

```


>>>

ルート定義

<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;

// 連絡先登録API
Route::post('contacts', 'ContactController@store');
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

コントローラー

<p style="font-size: 20px">`-r`で必要最低限のCRUDメソッドが記述済みのファイルが作成されます</p>

```bash 
# 既にphpコンテナに入ってる場合は不必要
$ docker exec -it php bash

[phpコンテナ内]$ php artisan make:controller ContactController -r
```

>>>

<p style="font-size: 20px">モデル(Eloquentなど)は特定のデータベーステーブルと対応します。</p>
<p style="font-size: 20px">これはデータの取得するのに開発者がSQLを意識しなくてもPHP風に操作することを可能にします</p>
<p style="font-size: 20px; color: green; ">app/Http/Controllers/ContactController.php</p>

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Requests\SaveContactRequest; // 追加
use App\Models\Contact; // 追加

class ContactController extends Controller
{
    /* 中略 */

    public function store(SaveContactRequest $request)
    {
        $contacts = $request->contacts;

        // contactテーブルにリクエストデータを挿入
        Contact::create($contacts);

        // json形式でレスポンスを返却
        return response()->json();
    }
}
```

>>>

APIの挙動を確認する

<p style="font-size: 20px">RestAPI開発するときに確認のためにrestClientを使用することがあります</p>
<p style="font-size: 20px">今回は導入が楽なChromeの拡張機能、`Yet Another Rest Client`を使用します。</p>

- [postman](https://www.postman.com/)
- [Yet Another Rest Client](https://chrome.google.com/webstore/detail/yet-another-rest-client/ehafadccdcdedbhcbddihehiodgcddpl)

>>>

以下の内容でリクエストを送信してください

200返却で正常です！
<p style="font-size: 20px">URL: http://localhost:8000/api/contacts</p>

```
<!-- payload -->

{
    "contacts": {
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

<img src="./test/examples/assets/restclient.png" style="width: 800px;">


