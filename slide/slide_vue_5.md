
## axios導入 (8)
<p style="font-size: 30px">Laravelで作成したAPIから連絡先のデータを取得しよう</p>

>>>

axiosインストール

```bash
[appコンテナ]$ cd frontend

[appコンテナ]$ npm install axios 
```

>>>

一覧ページでデータを取得する
<p style="font-size: 30px; color: green; ">frontend/src/pages/contact/index-contact.vue</p>

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
Access to XMLHttpRequest at ‘http://localhost:8000/api/contacts’ from origin ‘http://localhost:8080’ has been blocked by CORS policy: No ‘Access-Control-Allow-Origin’ header is present on the requested resource.
```

>>>

LaravelでCORSミドルウェア作成
