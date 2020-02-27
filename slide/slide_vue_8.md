## 一覧画面の仕上げ (11)
<p style="font-size: 20px">編集画面への遷移/削除機能/ローディングの実装を行います</p>

>>>

<p style="font-size: 30px">一覧画面から編集画面に遷移</p>
<p style="font-size: 20px; color: green; ">vue/src/pages/contact/index-contact.vue</p>

```html
<!-- 中略 -->
<el-table-column prop="id" label="操作" width="100">
<template slot-scope="scope">
    <el-button @click="editContact(scope.row.id)" type="primary" icon="el-icon-edit" circle></el-button>
    <el-button type="danger" icon="el-icon-delete" circle></el-button>
</template>
</el-table-column>
<!-- 中略 -->
```

>>>


<p style="font-size: 20px; color: green; ">vue/src/pages/contact/index-contact.vue</p>

```javascript
mathods: {
    // 中略
    editContact(id) {
      this.$router.push({name: 'edit_contact', params: { id: id }})
    }
    // 中略
}
```