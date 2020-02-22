
## 連絡先一覧ページ (5)
<span style="font-size: 30px">今回はElementUIというUIコンポーネントでサクッと実装します</span>

>>>

ElementUIのインストール

```bash
[appコンテナ内]$ npm install element-ui

```

>>>

ElementUIの導入

<p style="font-size: 30px; color: green; ">rontend/src/main.js</p>

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import ElementUI from 'element-ui' // 追記
import 'element-ui/lib/theme-chalk/index.css'　// 追記

Vue.use(ElementUI, { size: 'small'})　// 追記

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
```

>>>

ページの作成
<p style="font-size: 30px; color: green; ">frontend/src/pages/contact/index-contact.vue</p>

```html
<template>
  <el-container 
    style="border: 1px solid #eee; min-height:100vh;"
  >

      <el-header style="text-align: right; font-size: 12px">
        <el-dropdown>
            <i class="el-icon-setting" style="margin-right: 15px"></i>
            <el-dropdown-menu slot="dropdown">
            <el-dropdown-item>View</el-dropdown-item>
            <el-dropdown-item>Add</el-dropdown-item>
            <el-dropdown-item>Delete</el-dropdown-item>
            </el-dropdown-menu>
        </el-dropdown>
      </el-header>
    
    <el-container>
        <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
        <el-menu 
        :default-openeds="['1', '3']"
        mode="history"
        router
        >
            <el-submenu index="1">
            <template slot="title"><i class="el-icon-message"></i>連絡先登録アプリ</template>
            <el-menu-item-group>
                <template slot="title">基本機能</template>
                <el-menu-item index="1-1">連絡先を登録する</el-menu-item>
            </el-menu-item-group>
            </el-submenu>
        </el-menu>
        </el-aside>
        <el-main>
            <el-table
            :data="contacts" 
            style="width: 100%"
            empty-text="データが存在しません"
            >
            <el-table-column prop="last_name" label="姓" width="140">
            </el-table-column>
            <el-table-column prop="first_name" label="名" width="120">
            </el-table-column>
            <el-table-column prop="gender" label="性別" width="100" :formatter="genderFormatter">
            </el-table-column>
            <el-table-column prop="phone_number" label="電話番号" width="140">
            </el-table-column>
            <el-table-column prop="email" label="Eメール" width="160">
            </el-table-column>
            <el-table-column prop="address" label="住所" width="200">
            </el-table-column>
            <el-table-column prop="birthday" label="誕生日" width="140">
            </el-table-column>
            <el-table-column prop="id" label="操作" width="100">
                <template>
                <el-button type="primary" icon="el-icon-edit" circle></el-button>
                <el-button type="danger" icon="el-icon-delete" circle></el-button>
                </template>
            </el-table-column>
            </el-table>
        </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      contacts: [{
            last_name: '山田',
            first_name: '真司',
            gender: '1',
            phone_number: '080xxxxxxxx',
            email: 'yamada@gmail.com',
            address: '東京都江戸川区',
            birthday: '1990-06-01',
          },{
            last_name: '斎藤',
            first_name: '義一',
            gender: '1',
            phone_number: '080xxxxxxxx',
            email: 'saito@gmail.com',
            address: '東京都江戸川区',
            birthday: '1990-06-01',
          },{
            last_name: '山田',
            first_name: '佳代子',
            gender: '2',
            phone_number: '080xxxxxxxx',
            email: 'kayo@gmail.com',
            address: '東京都江戸川区',
            birthday: '1997-06-01',
          }]
    }
  },
  methods: {
    genderFormatter(row) {
      let gender = ''

      switch(row.gender) {
        case 1:
          gender = '男性'
          break
        case 2:
          gender = '女性'
          break
        default:
          gender = '両方'
          break
      }

      return gender
    },
  }
}
</script>

<style>
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 60px;
  }

  .el-aside {
    color: #333;
  }
</style>
```

>>>

ブラウザで確認してみよう
