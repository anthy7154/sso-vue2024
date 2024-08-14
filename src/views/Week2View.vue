<template>
  <div>
    <h2>註冊</h2>
    <input type="email" placeholder="Email" v-model="signupField.email">
    <input type="password" placeholder="Password" v-model="signupField.password">
    <input type="text" placeholder="Nickname" v-model="signupField.nickname">
    <!-- {{ signupField }} -->
    <button type="button" @click="signup">Sign Up</button>
    <p>{{ signupRes }}</p>
  </div>

  <div>
    <h2>登入</h2>
    <input type="email" placeholder="Email" v-model="signInField.email">
    <input type="password" placeholder="Password" v-model="signInField.password">
    <!-- {{ signInField }} -->
    <button type="button" @click="signIn">Sign In</button>
    <p>{{ signInRes }}</p>
  </div>

  <div>
    <h2>驗證</h2>
    <input type="text" placeholder="Token" v-model="tokenCheckout">
    <button type="button" @click="checkout">Checkout</button>
    <div v-if="user">
      <p>UID: {{ user.uid }}</p>
      <p>NICKNAME: {{ user.nickname }}</p>
    </div>
  </div>


  <div>
    <h2>登出</h2>
    <input type="text" placeholder="Token" v-model="tokenSignOut">
    <button type="button" @click="signOut">Sign Out</button>
    <p>{{ signOutRes }}</p>
  </div>

  <hr>
  <h2>Todo-List</h2>
  <div v-if="token">
    <input type="text" placeholder="New Todo" v-model="newTodo">
    <button type="button" @click="addTodo">Add todo</button>
    <div>
      <!-- <ul v-for="(todo ) in todos" v-bind="">

      </ul> -->
    </div>

  </div>

</template>

<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";

const api = "https://todolist-api.hexschool.io"

// 註冊
const signupField = ref({
  email: "",
  password: "",
  nickname: ""
})

const signupRes = ref("");
const signup = async () => {
  try {
    // console.log(`${api}/users/sign_up`);

    const res = await axios.post(`${api}/users/sign_up`,
      signupField.value
    );
    // console.log(res);
    signupRes.value = `註冊成功, UID: ${res.data.uid}`

  } catch (error) {
    console.log(error)
    signupRes.value = `註冊失敗, ${error.message}`
  }
}

// 登入
const signInField = ref({
  email: "",
  password: "",
});

const token = ref("")
const signInRes = ref("")

const signIn = async () => {
  try {
    // console.log(`${api}/users/sign_in`);
    const res = await axios.post(`${api}/users/sign_in`,
      signInField.value
    );
    // console.log(res);
    signInRes.value = `登入成功, ${res.data.token}`;
    token.value = res.data.token

    // 將 token 存入 cookie
    const tomorrow = new Date();
    tomorrow.setDate(tomorrow.getDate() + 1);
    document.cookie = `hexschoolTodo=${res.data.token}; expires=${tomorrow.toUTCString()}`;

    getTodos();

  } catch (error) {
    // console.log(error.message)
    signInRes.value = `登入失敗, ${error.message}`
  }
}

// 驗證
const user = ref({
  nickname: '',
  uid: ''
});

const tokenCheckout = ref("")
const checkout = async () => {
  try {
    const res = await axios.get(`${api}/users/checkout`, {
      headers: {
        authorization: tokenCheckout.value
      }
    });
    console.log(res);

    user.value = res.data
  } catch (error) {
    console.log(error.message)
  }
};

// 登出
const tokenSignOut = ref("");
const signOutRes = ref("");

const signOut = async () => {
  try {
    // console.log(`${api}/users/sign_out`);

    // url, data, headers
    const res = await axios.post(`${api}/users/sign_out`,
      {},
      {
        headers: {
          authorization: tokenSignOut.value
        }
      });
    console.log(res);
    signOutRes.value = res.data.message;

    tokenCheckout.value = ""
    user.value = !user.value

    // document.cookie.removeItem("hexschoolTodo");

  } catch (error) {
    console.log(error)
    signOutRes.value = `登出失敗, ${error.message}`;
  }
};

// ToDo List
const todos = ref([])
const newTodo = ref("")
// const todoEdit = ref("")

const getTodos = async () => {
  try {
    // console.log(todos.value)
    const res = await axios.get(`${api}/todos`, {
      headers: {
        authorization: token.value
      }
    })
    // console.log(res.data)
    todos.value = res.data
  } catch (error) {
    console.log(error)
  }
}

const addTodo = async () => {
  try {
    const res = await axios.post(`${api}/todos/`,
      newTodo.value,
      {
        headers: {
          authorization: token.value
        }
      })
    console.log(res)
    getTodos()
  } catch (error) {
    console.log(error)
  }
}

onMounted(() => {
  token.value = document.cookie.replace(
    /(?:(?:^|.*;\s*)hexschoolTodo\s*=\s*([^;]*).*$)|^.*$/,
    "$1",
  );
  // console.log(token)

  if (token.value) {
    tokenCheckout.value = token.value
    checkout()

    getTodos()
  }
});



</script>

<style scoped></style>