
## 一覧取得/更新/削除API (4)

>>>

一覧取得API

>>>

ルート定義
<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;

Route::post('contacts', 'ContactController@store');
Route::get('contacts', 'ContactController@index'); // 追記
```

>>>

コントローラー
<p style="font-size: 20px; color: green; ">app/Http/Controllers/Auth/ContactController.php</p>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\SaveContactRequest;
use App\Models\Contact;

class ContactController extends Controller
{
    /* 中略 */

    public function index()
    {
        $contacts = Contact::all();

        return response()->json([
            'contacts' => $contacts
        ]);
    }
}
```

>>>


連絡先削除API

>>>

ルート定義
<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;

Route::post('contacts', 'ContactController@store');
Route::delete('contacts/{id}', 'ContactController@delete'); // 追記
```

>>>

コントローラー
<p style="font-size: 20px; color: green; ">app/Http/Controllers/Auth/ContactController.php</p>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\SaveContactRequest;
use App\Models\Contact;

class ContactController extends Controller
{
    /* 中略 */

    public function destroy($id)
    {
        Contact::where('id',$id)->delete();

        return response()->json();
    }
}
```


>>>


連絡先編集API

>>>

ルート定義
<p style="font-size: 20px; color: green; ">routes/api.php</p>

```php
<?php

use Illuminate\Http\Request;

Route::get('contacts', 'ContactController@index');
Route::post('contacts', 'ContactController@store');
Route::delete('contacts/{id}', 'ContactController@destroy'); 
Route::get('contacts/{id}', 'ContactController@edit'); // 追記
Route::put('contacts/{id}', 'ContactController@update');　// 追記
```

>>>

コントローラー
<p style="font-size: 20px; color: green; ">app/Http/Controllers/Auth/ContactController.php</p>

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\SaveContactRequest;
use App\Models\Contact;

class ContactController extends Controller
{
    /* 中略 */

    public function edit($id)
    {
        $contacts = Contact::find($id);

        return response()->json([
            'contacts' => $contacts
        ]);
    }

    // Request -> SaveContactRequest
    public function update(SaveContactRequest $request, int $id)
    {
        $contact = Contact::find($id);
        $contact->update($request->contacts);

        return response()->json();
    }
}
```

>>>

Yet Another Rest Clientで一通り挙動確認してみよう
