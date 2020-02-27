## Laravel + Vueで連絡先アプリケーション開発！

crowish勉強会


---

## 自己紹介

<p style="font-size: 30px; text-align: left;">・かみまこと @kaminotsukai (kmmk)</p>
<p style="font-size: 30px; text-align: left;">・Laravel + Vueでお仕事してます</p>
<p style="font-size: 30px; text-align: left;">・みんなでわいわいする会好き（特にLT）</p>
<p style="font-size: 30px; text-align: left;">・趣味はバスケとボルダリングとカラオケ</p>
<p style="font-size: 30px; text-align: left;">・google io本場で見たい</p>


---

## 今回の流れ

>>>


- イントロダクション
- Dockerで環境構築
- LaravelでAPI開発
- Vueでフロントエンド開発
- これから(懇親会)
- リファクタ(時間が余れば)

>>>

今回の目的

- LaravelとVueを繋げる方法を理解する
- Laravel + Vueの基本的な機能を知ることができる
- 必要最低限のCRUDの実装ができるようになる
- axiosを使ったAPI通信の実装ができるようになる
- corsを理解する

---

## Dockerで環境を構築する
<p style="font-size: 20px ;">環境構築後は各自スピードが異なる可能性があるので、自分のペースで進めていただいて構いません</p>

>>>

### ファイル構成

<p style="font-size: 20px ;">以降編集するLaravel/Vueのファイルはホスト側にマウントされているので、vscodeなどで編集可能です</p>

```
laraVue-docker-compose
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

5つのコンテナが起動していることが確認できればOK！
STATUS              PORTS                 
Up 52 seconds       0.0.0.0:1234->80/tcp  
Up 52 seconds       0.0.0.0:80->80/tcp    
Up 54 seconds       9000/tcp              
:
```

>>>

今回のDockerの構成

<img src="./test/examples/assets/laravel.png" style="width: 1000px;">

>>>

## Q&A

>>>

#### Q1. docker-compose のバージョンが違う

<p style="font-size: 20px">A. 以下のコマンドで1.25.3のdocker-composeを取得してください</p>

```bash 
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```


>>>

確認
<p style="font-size: 30px"><a href="http://localhost:1234" target="_blank">http://localhost:1234</a></p>
<p style="font-size: 20px">以下のような表示がされれば正常に動いています。</p>

<img src="./test/examples/assets/pma.png" style="width: 800px;">


>>>

Dockerの基本操作

```bash
# コンテナに入っていないことを確認してください
# プロンプトが`#`と表記されている場合はコンテナに入っているので `exit`でコンテナから抜けてください

# 1. laravelの作業をする場合
$ docker exec -it php bash

# 2. vueの作業をする場合
$ docker exec -it app sh
```

>>>


<p style="font-size: 20px ;">今回はDockerを使用するため、どのコンテナで作業するか明確にするために以下のような表示をします</p>

```bash
# Laravelの作業
[phpコンテナ] $ php artisan migrate

# Vueの作業
[appコンテナ] $ npm run serve
```
