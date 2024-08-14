<template>
  <div>
    <h1>註冊</h1>
    <input type="email" placeholder="Email" v-model="signupField.email">
    <input type="password" placeholder="Password" v-model="signupField.password">
    <input type="text" placeholder="Nickname" v-model="signupField.nickname">
    <br>
    {{ signupField }}
    <button type="button" @click="signup">Sign Up</button>
    UID: {{ signupRes }}
  </div>

  <div>
    <h1>登入</h1>
    <input type="email" placeholder="Email" v-model="signInField.email">
    <input type="password" placeholder="Password" v-model="signInField.password">
    <br>
    {{ signInField }}
    <button type="button" @click="signIn">Sign In</button>
    Token: {{ signInRes }}
  </div>

  <div>
    <h1>驗證</h1>
    <div v-if="user.uid">
      <p>UID: {{ user.uid }}</p>
      <p>Nickname: {{ user.nickname }}</p>
    </div>
    <div v-else>
      你還沒有登入哦
    </div>
  </div>


  <h1>登出</h1>
  <input type="text" placeholder="Token">
  <button type="button">Sign Out</button>

</template>

<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";

const api = "https://todolist-api.hexschool.io"

const signupField = ref({
  email: "",
  password: "",
  nickname: ""
})

const signupRes = ref("")

const signup = async () => {
  try {
    console.log(`${api}/users/sign_up`);

    const res = await axios.post(`${api}/users/sign_up`,
      signupField.value
    );
    console.log(res);
    signupRes.value = res.data.uid;

  } catch (error) {
    console.log(error)
  }
}

// 登入

const signInField = ref({
  email: "",
  password: "",
})

const signInRes = ref("")

const signIn = async () => {
  try {
    console.log(`${api}/users/sign_In`);

    const res = await axios.post(`${api}/users/sign_In`,
      signInField.value
    );
    console.log(res);
    signInRes.value = res.data.token;

    const tomorrow = new Date();
    tomorrow.setDate(tomorrow.getDate() + 1);
    document.cookie = `hexschoolTodo=${res.data.token}; expires=${tomorrow.toUTCString()}`;

  } catch (error) {
    console.log(error)
  }
}

const user = ref({
  nickname: '',
  uid: ''
})

onMounted(async () => {

  const token = document.cookie.replace(
    /(?:(?:^|.*;\s*)hexschoolTodo\s*=\s*([^;]*).*$)|^.*$/,
    "$1",
  );

  console.log(token)

  const res = await axios.get(`${api}/users/checkout`, {
    headers: {
      authorization: token
    }
  });
  console.log(res);

  user.value = res.data
})

</script>

<style scoped></style>