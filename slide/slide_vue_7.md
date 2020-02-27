
## 連絡先編集ページ (10)
<p style="font-size: 30px">今回は保存と編集のページは同じUIのため一つのファイルで完結させる方が効率的です</p>

>>>

連絡先登録ページと同じファイル
<p style="font-size: 20px; color: green; ">vue/src/pages/contact/save-contact.vue</p>

```html
<!--  一部抜粋してます -->
<el-form-item>
    <!-- 登録　=> saveContactTitle -->
    <!-- axiosSaveContact => saveContactに変更 -->
    <el-button type="primary" @click="saveContact()">{{ saveContactTitle }}</el-button>
</el-form-item>
```

>>>

スクリプト部分
<p style="font-size: 30px">URLで編集か登録かを判別して、それぞれの処理を書いていきます</p>
<p style="font-size: 20px; color: green; ">vue/src/pages/contact/save-contact.vue</p>

```javascript
import Sidebar from '../../components/sidebar'
import axios from 'axios'
const BASE_URL = 'http://localhost:8000'

export default {
  components: {
    Sidebar,
  },
  data() {/* 省略 */}
  computed: {
      newContact() {
           const routeParam = this.$route.params.id + ''
           return !routeParam.match(/^\d+$/)
      },
      saveContactTitle() {
          return this.newContact ? '登録' : '更新'
      },
      id () {
          return this.$route.params.id
      }
  },
  methods: {
      axiosSaveContact() {/* 省略 */},
      axiosGetContact(id) {/* 省略 */},
      axiosUpdateContact(id) {
          const endPoint = BASE_URL + `/api/contacts/${id}`
          const params = {
              'contacts': this.contacts
          }

          axios.put(endPoint, params, {headers: {'Content-Type': 'application/json'}})
            .then(res => {
                console.log(res)
                this.$router.push({name: 'contact'})
            })
            .catch(error => {
                this.error(error.response.data.errors)
                console.warn(error)
            })
      },
      saveContact() {
          this.loading = true
          const id = this.id

          if (this.newContact) {
              this.axiosSaveContact()
          } else {
              this.axiosUpdateContact(id)
          }
      },
      error(errors) {/* 省略 */}
  },
  created() {
      if (!this.newContact) {
          this.axiosGetContact(this.id)
      }
  } 
}
```