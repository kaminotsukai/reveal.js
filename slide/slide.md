## Laravel + Vueで連絡先アプリケーション開発！

crowish勉強会


---

## 自己紹介

<p style="font-size: 30px; text-align: left;">・かみまこと @kaminotsukai (kmmk)</p>
<p style="font-size: 30px; text-align: left;">・大阪出身</p>
<p style="font-size: 30px; text-align: left;">・新しい物好き</p>
<p style="font-size: 30px; text-align: left;">・Laravel + Vueでお仕事します</p>
<p style="font-size: 30px; text-align: left;">・趣味はバスケとボルダリングとカラオケ</p>
<p style="font-size: 30px; text-align: left;">・最近Python面白そうってなってる</p>



---

## 目次

>>>

- 作成するミニアプリのデモ
- Dockerで環境構築
- LaravelでAPI開発
- Vue.jsで画面開発

---

## Dockerで環境を構築する (1)
<p style="font-size: 20px ;">環境構築後は各自スピードが異なる可能性があるので、自分のペースで進めていただいて構いません</p>

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
