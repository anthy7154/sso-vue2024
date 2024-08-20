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
    {{ tokenCheckout }}
    <button type="button" @click="checkout">Checkout</button>
    <p>{{ checkoutRes }}</p>
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
      <ul v-for="(todo) in todos" v-bind:key="todo.id">
        <li>
          任務內容：{{ todo.content }} | 狀態：{{ todo.status ? "完成" : "未完成" }} |
          <input type="text" v-model="todoEdit[todo.id]" placeholder="edit content">
          <button type="button" @click="deleteTodo">Delete</button>
          <button type="button" @click="updateTodo(todo.id)">Update</button>
          <button type="button" @click="toggleTodoStatus">Toggle Status</button>
        </li>
        <!-- | <input type="text" v-model="tod"> -->
      </ul>
    </div>

  </div>

</template>

<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";

const api = "https://todolist-api.hexschool.io"

// 註冊 ==============================
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

// 登入 ==============================
const signInField = ref({
  email: "",
  password: "",
});

const token = ref("") // 登入後取得的 token
const signInRes = ref("")

// 登入後會回傳 token
const signIn = async () => {
  try {
    // console.log(`${api}/users/sign_in`);
    const res = await axios.post(`${api}/users/sign_in`,
      signInField.value
    );
    // console.log(res);
    signInRes.value = `登入成功, ${res.data.token}`;

    // TODO
    token.value = res.data.token

    // tokenCheckout.value = res.data.token
    // checkout()
    // getTodos();

  } catch (error) {
    // console.log(error.message)
    signInRes.value = `登入失敗, ${error.message}`
  }
}

// 驗證 ==============================
const tokenCheckout = ref("")
const checkoutRes = ref("")

// 使用登入後回傳的 token 去驗證，並存入 document.cookie
const checkout = async () => {
  try {
    const res = await axios.get(`${api}/users/checkout`, {
      headers: {
        authorization: tokenCheckout.value
      }
    });
    console.log(res);

    // user.value = res.data
    checkoutRes.value = `驗證成功, UID: ${res.data.uid}, Nickname: ${res.data.nickname}`

    // 將 token 存入 cookie
    const tomorrow = new Date();
    tomorrow.setDate(tomorrow.getDate() + 1);
    document.cookie = `hexschoolTodo=${tokenCheckout.value}; expires=${tomorrow.toUTCString()}`;

    // 驗證成功後，將已驗證的 token 拿去執行 getTodos()
    token.value = tokenCheckout.value
    getTodos()

  } catch (error) {
    console.log(error.message)
    checkoutRes.value = `驗證失敗, ${error.message}`
  }
};

// 登出 ==============================
// signout 時，API 那端會將該組 cookie 失效，不能再用於 chekcout，如果一開始沒先過 checkout 就 signout 那會驗證不過 
// 但失效的 cookie 還是能 signout 登出成功 (應該還存在伺服器)
const tokenSignOut = ref("");
const signOutRes = ref("");

const signOut = async () => {
  try {
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
    tokenSignOut.value = ""
    token.value = ""

  } catch (error) {
    console.log(error)
    signOutRes.value = `登出失敗, ${error.message}`;
  }
};

// ToDo List ==============================
const todos = ref([])
const newTodo = ref("")

// 取得所有 todos ==============================
const getTodos = async () => {
  try {
    // console.log(todos.value)
    const res = await axios.get(`${api}/todos`, {
      headers: {
        authorization: token.value
      }
    })
    // console.log(res.data)
    todos.value = res.data.data
    // console.log("todos:", todos.value)

  } catch (error) {
    console.log(error)
  }
}

// 新增 todo ==============================
const addTodo = async () => {
  try {
    // console.log(newTodo.value)

    await axios.post(`${api}/todos/`,
      {
        content: newTodo.value
      },
      {
        headers: {
          authorization: token.value
        }
      })
    newTodo.value = ""
    getTodos()

  } catch (error) {
    console.log(error)
  }
}

// 修改 todo ==============================
const todoEdit = ref({});

const updateTodo = async (todoId) => {
  try {
    // const todo = todos.value.find((todo) => todo.id === todoId);
    // todo.content = todoEdit.value[todoId];
    // const updateTodoRes = await axios.put(`${api}/todos/${todoId}`, todo,
    const updateTodoRes = await axios.put(`${api}/todos/${todoId}`,
      {
        content: todoEdit.value[todoId]
      }, {
      headers: {
        authorization: token.value
      }
    })
    console.log(updateTodoRes)

    todoEdit.value[todoId] = ""
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