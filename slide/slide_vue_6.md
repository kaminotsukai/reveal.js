
## 連絡先登録ページ (9)

>>>

連絡先登録画面
<p style="font-size: 30px; color: green; ">frontend/src/pages/contact/save-contact.vue</p>

```html
<template>
  <div>
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
            :default-active="activeIndex"
            mode="history"
            router
            >
            <el-submenu index="1">
            <template slot="title"><i class="el-icon-message"></i>連絡先登録アプリ</template>
            <el-menu-item-group>
                <template slot="title">基本機能</template>
                <el-menu-item index="1-1" :route="{ name: 'create_contact' }">連絡先を登録する</el-menu-item>
            </el-menu-item-group>
            </el-submenu>
        </el-menu>
        </el-aside>
            <el-main>
                <div style="width:800px; margin: auto; margin-top: 50px;">
                    <div class="error" style="color: red;">
                        {{ errorMsg }}
                    </div>
                    <span style="color: red; font-size: 8px;">※は必須です</span><br>
                    <el-form :model="contacts" :rules="rules" ref="contacts" label-width="120px" class="demo-ruleForm">
                        <el-form-item label="姓" prop="last_name" required>
                            <el-input v-model="contacts.last_name"></el-input>
                        </el-form-item>
                        <el-form-item label="名" prop="first_name" required>
                            <el-input v-model="contacts.first_name"></el-input>
                        </el-form-item>
                        <el-form-item label="性別" prop="gender" required>
                            <el-radio-group v-model="contacts.gender">
                            <el-radio :label="1">男性</el-radio>
                            <el-radio :label="2">女性</el-radio>
                            <el-radio :label="3">両方</el-radio>
                            </el-radio-group>
                        </el-form-item>
                        <span style="color: red; font-size: 8px;">ハイフンなしで入力してください</span>
                        <el-form-item label="電話番号(携帯)" prop="phone_number" required>
                            <el-input placeholder="例) 08011112222" v-model="contacts.phone_number"></el-input>
                        </el-form-item>
                        <el-form-item label="電話番号(自宅)" prop="house_phone_number">
                            <el-input placeholder="例) 08011112222" v-model="contacts.house_phone_number"></el-input>
                        </el-form-item>
                        <el-form-item label="Eメール" prop="email">
                            <el-input v-model="contacts.email"></el-input>
                        </el-form-item>
                        <el-form-item label="住所" prop="address">
                            <el-input v-model="contacts.address"></el-input>
                        </el-form-item>
                        <el-form-item label="誕生日">
                            <el-col :span="11">
                            <el-form-item prop="birthday">
                                <el-date-picker 
                                    type="date" 
                                    v-model="contacts.birthday" 
                                    style="width: 100%;"
                                    value-format="yyyy-MM-dd"
                                ></el-date-picker>
                            </el-form-item>
                            </el-col>
                        </el-form-item>
                        <el-form-item label="メモ" prop="memo">
                            <el-input type="textarea" v-model="contacts.memo"></el-input>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" @click="saveContact()">登録</el-button>
                        </el-form-item>
                    </el-form>
                </div>
            </el-main>
        </el-container>
    </el-container>
  </div>
</template>

<script>
import axios from 'axios'
const BASE_URL = 'http://localhost:8000'

export default {
  data() {
    return {
      contacts: {
          'last_name': '',
          'first_name': '',
          'phone_number': '',
          'house_phone_number': '',
          'email': '',
          'address': '',
          'birthday': '',
          'memo': '',
          'gender': 1,
      },
      errorMsg: '',
      rules: {
          last_name: [
            { required: true, message: '必須項目です' },
            { max: 30, message: '30文字以内で入力してください。' },
          ],
          first_name: [
            { required: true, message: '必須項目です' },
            { max: 30, message: '30文字以内で入力してください。' },
          ],
          phone_number: [
            { required: true, message: '必須項目です' },
          ],
          memo: [
            { max: 300, message: '300文字以内で入力してください。' },
          ],
      }
    }
  },
  methods: {
      axiosSaveContact() {
          const endPoint = BASE_URL + '/api/contacts'
          const params = {
              'contacts': this.contacts
          }

          axios.post(endPoint, params, {headers: {'Content-Type': 'application/json'}})
            .then(res => {
                this.$router.push({name: 'contact'})
            })
            .catch(error => {
                this.error(error.response.data.errors)
                console.warn(error)
            })
            .finally(() => {
                this.loading = false
            })
      },
      axiosGetContact(id) {
          const endPoint = BASE_URL + `/api/contacts/${id}`
          
          axios.get(endPoint)
            .then(res => {
                this.contacts = res.data.contacts
            })
            .catch(error => {
                console.warn(error)
            })
      },
      error(errors) {
          this.errorMsg = errors['contacts.phone_number'][0];
      }
  }
}
</script>
```
