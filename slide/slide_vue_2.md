
## Vueのセットアップ (5)

<p style="font-size: 30px">Vueプロジェクトの作成</p>


>>>

Vue.jsプロジェクトの作成

```bash
// phpコンテナに入ってる場合は抜ける
[phpコンテナ] exit

// appコンテナに入る
$ docker exec -it app sh

// vueプロジェクト作成
[appコンテナ]$ vue create vue

? Please pick a preset: default (babel, eslint) 
❯ default
  other

? Pick the package manager to use when installing dependencies: (Use arrow keys)
  Use Yarn 
❯ Use NPM 

[appコンテナ]$ cd vue

[appコンテナ]$ npm run serve
```


>>>

Vueが立ち上がっているか確認

<a href="http://localhost:8080" target="_blank">http://localhost:8080</a>
