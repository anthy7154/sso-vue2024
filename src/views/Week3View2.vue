<template>
  <div id="root">
    <div class="container mt-5">
      <div class="row">
        <!-- 菜單 -->
        <div class="col-md-4">
          <!-- <div class="list-group" v-for="drink in drinks" :key="drink.id">
            <a href="#" class="list-group-item list-group-item-action" @click.prevent="addCart(drink.id)">
              {{ drink.id }}
              <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">{{ drink.name }}</h5>
                <small>${{ drink.price }}</small>
              </div>
              <p class="mb-1">{{ drink.description }}</p>
            </a>
          </div> -->
          <ItemMenu :drinks="drinks" @add-cart="addCart" />
        </div>

        <!-- 購物車 -->
        <div class="col-md-8">
          <!-- <table class="table">
            <thead>
              <tr>
                <th scope="col" width="50">操作</th>
                <th scope="col">品項</th>
                <th scope="col">描述</th>
                <th scope="col" width="90">數量</th>
                <th scope="col">單價</th>
                <th scope="col">小計</th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="item in cart" :key="item.id">
                <td><button type="button" class="btn btn-sm" @click="removeCart(item.id)">x</button></td>
                <td>{{ item.name }}</td>
                <td><small>{{ item.description }}</small></td>
                <td>
                  <select class="form-select" v-model="item.quantity">
                    <option v-for="i in 10" :key="i" :value="i">{{ i }}</option>
                  </select>
                </td>
                <td>{{ item.price }}</td>
                <td>{{ item.price * item.quantity }}</td>
              </tr>
            </tbody>
          </table> -->
          <ItemCart :cart="cart" @remove-cart="removeCart" />

          <div class="text-end mb-3">
            <h5>總計: <span>${{ sum }}</span></h5>
          </div>
          <textarea class="form-control mb-3" rows="3" placeholder="備註" v-model="orderNote"></textarea>
          <div class="text-end">
            <button class="btn btn-primary" @click="newOrder()">送出</button>
          </div>
        </div>
      </div>
      <hr />

      <!-- 訂單 -->
      <div class="row justify-content-center">
        <div class="col-8">
          <div class="card">
            <div class="card-body">
              <div class="card-title">
                <h5>訂單</h5>
                <!-- <table class="table">
                  <thead>
                    <tr>
                      <th scope="col">品項</th>
                      <th scope="col">數量</th>
                      <th scope="col">小計</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in order.cart" :key="item.id">
                      <td>{{ item.name }}</td>
                      <td>{{ item.quantity }}</td>
                      <td>{{ item.price * item.quantity }}</td>
                    </tr>
                  </tbody>
                </table>
                <div class="text-end">備註: <span>{{ order.orderNote }}</span></div>
                <div class="text-end">
                  <h5>總計: <span>${{ order.sum }}</span></h5>
                </div> -->
                <ItemOrder :order="order" />
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="toast-container position-fixed top-50 start-50 translate-middle">
        <div id="message" class="toast" role="alert" aria-live="assertive" aria-atomic="true">
          <div class="toast-body" style="background-color: #fa0; text-align: center;">
            <p class="h5">
              您已經將該品項加入購物車
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';
import ItemMenu from '@/components/ItemMenu.vue';
import ItemCart from '@/components/ItemCart.vue';
import ItemOrder from '@/components/ItemOrder.vue';

const drinks = ref([
  { "id": 1, "name": "珍珠奶茶", "description": "香濃奶茶搭配QQ珍珠", "price": 50 },
  { "id": 2, "name": "冬瓜檸檬", "description": "清新冬瓜配上新鮮檸檬", "price": 45 },
  { "id": 3, "name": "翡翠檸檬", "description": "綠茶與檸檬的完美結合", "price": 55 },
  { "id": 4, "name": "四季春茶", "description": "香醇四季春茶，回甘無比", "price": 45 },
  { "id": 5, "name": "阿薩姆奶茶", "description": "阿薩姆紅茶搭配香醇鮮奶", "price": 50 },
  { "id": 6, "name": "檸檬冰茶", "description": "檸檬與冰茶的清新組合", "price": 45 },
  { "id": 7, "name": "芒果綠茶", "description": "芒果與綠茶的獨特風味", "price": 55 },
  { "id": 8, "name": "抹茶拿鐵", "description": "抹茶與鮮奶的絕配", "price": 60 },
])
const cart = ref([])
const order = ref({})
const orderNote = ref("")
const sum = computed(() => {
  return cart.value.reduce((pre, cur) => {
    return pre + (cur.price * parseInt(cur.quantity))
  }, 0)
})

const tempCart = ref([])
// 將品項加入購物車
const addCart = (itemId) => {
  const findDrink = drinks.value.findIndex(drink => drink.id === itemId)

  // 取出目前購物車內所有品項的名稱
  cart.value.forEach((item) => {
    tempCart.value.push(item["name"])
    // console.log(item)
  })

  // 如果清單(從購物車取出)不包含使用者最新一次點擊的品項名稱，就加入購物車
  if (!tempCart.value.includes(drinks.value[findDrink].name)) {
    cart.value.push({
      ...drinks.value[findDrink],
      "id": new Date().getTime(),
      "quantity": 1
    })
    // console.log("tempCart: ", tempCart.value)
  } else {
    const toastMessage = document.getElementById("message");
    toastMessage.classList.add("show");

    // 0.7 秒後關閉提示
    setTimeout(() => {
      toastMessage.classList.remove("show");
    }, 700);
  }
  tempCart.value = []
}

// 將品項移出購物車
const removeCart = (itemId) => {
  // console.log(itemId)
  cart.value = cart.value.filter(item => item.id !== itemId)
  // console.log(cart.value)
}

// 建立訂單
const newOrder = () => {
  order.value = {
    cart: cart.value,
    orderNote: orderNote.value,
    id: new Date().getTime(),
    sum: sum.value,
  }
  // 清空購物車及備註
  cart.value = [];
  orderNote.value = "";
}
</script>