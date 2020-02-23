
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

#### DBとの接続

>>>

<p style="font-size: 20px">backend直下にある`.env`というファイルを編集していきます。</p>
<p style="font-size: 20px">ここではDB接続情報などの環境変数を記述していきます。</p>
<p style="font-size: 20px; color: green; ">backend/.env</p>

```
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

