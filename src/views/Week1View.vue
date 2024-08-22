<script setup>
import { ref } from "vue";

const menu = ref([
  { id: 1, name: "珍珠奶茶", description: "香濃奶茶搭配QQ珍珠", price: 50, stock: 20 },
  { id: 2, name: "冬瓜檸檬", description: "清新冬瓜配上新鮮檸檬", price: 45, stock: 18 },
  { id: 3, name: "翡翠檸檬", description: "綠茶與檸檬的完美結合", price: 55, stock: 34 },
  { id: 4, name: "四季春茶", description: "香醇四季春茶，回甘無比", price: 45, stock: 10 },
  { id: 5, name: "阿薩姆奶茶", description: "阿薩姆紅茶搭配香醇鮮奶", price: 50, stock: 25 },
  { id: 6, name: "檸檬冰茶", description: "檸檬與冰茶的清新組合", price: 45, stock: 20 },
  { id: 7, name: "芒果綠茶", description: "芒果與綠茶的獨特風味", price: 55, stock: 18 },
  { id: 8, name: "抹茶拿鐵", description: "抹茶與鮮奶的絕配", price: 60, stock: 20 }
])

const updateStock = (item, condition) => {
  if (condition === "increase") {
    item.stock++;
  } else if (condition === "decrease" && item.stock > 0) {
    item.stock--;
  }
}

const editable = ref(false)
const tempItem = ref({
  id: 0,
  name: ""
})

// 啟用編輯功能
const handleEdit = (itemId) => {
  if (editable.value == false) {
    editable.value = true
  }

  // 透過 id 取得名稱，並記錄於 tempItem
  const index = menu.value.findIndex(item => item.id === itemId);
  tempItem.value.id = index;
  tempItem.value.name = menu.value[index].name;
}

// 將編輯的結果更正到菜單上
const finishEdit = () => {
  menu.value[tempItem.value.id].name = tempItem.value.name;
}

</script>

<template>
  <div class="container">
    <div class="row p-3">
      <div class="col-12">
        <h1>Vue week01 Homework</h1>

        <table class="table table-striped">
          <thead>
            <tr>
              <th scope="col">編號</th>
              <th scope="col">品項</th>
              <th scope="col">描述</th>
              <th scope="col">價格</th>
              <th scope="col">庫存</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in menu" v-bind:key="item.id">
              <th scope="row">{{ item.id }}</th>
              <td v-on:dblclick="handleEdit(item.id)">{{ item.name }}</td>
              <td><small>{{ item.description }}</small></td>
              <td>{{ item.price }}</td>
              <td>
                <button type="button" class="btn btn-sm btn-secondary" v-on:click="updateStock(item, 'decrease')"
                  v-bind:disabled="item.stock === 0">-</button>
                {{ item.stock }}
                <button type="button" class="btn btn-sm btn-secondary"
                  v-on:click="updateStock(item, 'increase')">+</button>
              </td>
            </tr>
          </tbody>
        </table>

        <p class="alert alert-info" role="alert">
          滑鼠雙擊其一品項名稱，以編輯內容
        </p>

        <div class="col-12" v-if="editable">

          <hr>
          <h2>編輯區域</h2>
          <input type="text" class="form-control" name="" id="" v-model="tempItem.name">
          <button type="button" class="btn btn-secondary" v-on:click="finishEdit"
            v-bind:disabled="tempItem.name === menu[tempItem.id].name || tempItem.name === ''">確認</button>
          <button type="button" class="btn btn-light" v-on:click="editable = false">取消</button>
        </div>
      </div>
    </div>
  </div>

</template>

<style scoped></style>