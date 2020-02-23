
## Laravelのセットアップ (2)

>>>

Laravelプロジェクトの作成

```bash 
# phpコンテナに入る(docker-compose.ymlがあるディレクトリで入力してください)
$ docker exec -it php bash 

# laravelプロジェクト作成
[phpコンテナ内]$ laravel new 

# 確認
[phpコンテナ内]$ ls

artisan    composer.json  config    package-lock.json  phpunit.xml  resources  server.php  tests   webpack.mix.js
app	   bootstrap  composer.lock  database  package.json	  public       routes	  storage     vendor
```

>>>

ロケールの設定
<p style="font-size: 20px; color: green; ">config/app.php</p>

```php
'locale' => 'ja',
```

>>>

#### DBとの接続

>>>

<p style="font-size: 20px">backend直下にある`.env`というファイルを編集していきます。</p>
<p style="font-size: 20px">ここではDB接続情報などの環境変数を記述していきます。</p>
<p style="font-size: 20px; color: green; ">backend/.env(一部)</p>

```php
DB_CONNECTION=mysql
DB_HOST=db-host 
DB_PORT=3306
DB_DATABASE=database 
DB_USERNAME=docker 
DB_PASSWORD=docker 
```

