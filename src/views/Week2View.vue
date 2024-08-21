<template>
  <div class="container-sm">
    <div class="row p-5">
      <div class="col-6 border-end">
        <form>
          <h2 class="text-decoration-underline">註冊</h2>
          <div class="mb-3">
            <label for="signupInputEmail" class="form-label">Email</label>
            <input type="email" class="form-control" id="signupInputEmail" v-model="signupField.email">
          </div>

          <div class="mb-3">
            <label for="signupInputPassword" class="form-label">Password</label>
            <input type="password" class="form-control" id="signupInputPassword" v-model="signupField.password">
          </div>

          <div class="mb-3">
            <label for="signupInputNickname" class="form-label">Nickname</label>
            <input type="text" class="form-control" id="signupInputNickname" v-model="signupField.nickname">
          </div>

          <p class="text-black-50 text-break border border-primary p-2" v-if="signupRes">{{ signupRes }}</p>
          <div class="mb-3 text-end">
            <button type="button" class="btn btn-secondary" @click="signup">Sign Up</button>
          </div>
        </form>

        <hr>

        <form>
          <h2 class="text-decoration-underline">登入</h2>
          <div class="mb-3">
            <label for="signInInputEmail" class="form-label">Email</label>
            <input type="email" class="form-control" id="signInInputEmail" v-model="signInField.email">
          </div>

          <div class="mb-3">
            <label for="signInInputPassword" class="form-label">Password</label>
            <input type="password" class="form-control" id="signInInputPassword" v-model="signInField.password">
          </div>

          <p class="text-black-50 text-break border border-primary p-2" v-if="signInRes">{{ signInRes }}</p>
          <div class="mb-3 text-end">
            <button type="button" class="btn btn-secondary" @click="signIn">Sign In</button>
          </div>
        </form>

        <hr>

        <form>
          <h2 class="text-decoration-underline">驗證</h2>
          <div class="mb-3">
            <label for="checkoutInputToken" class="form-label">Token</label>
            <input type="text" class="form-control" id="checkoutInputToken" v-model="tokenCheckout">
          </div>

          <p class="text-black-50 text-break border border-primary p-2" v-if="checkoutRes">{{ checkoutRes }}</p>
          <div class="mb-3 text-end">
            <button type="button" class="btn btn-secondary" @click="checkout">Checkout</button>
          </div>
        </form>

        <hr>

        <form>
          <h2 class="text-decoration-underline">登出</h2>
          <div class="mb-3">
            <label for="signOutInputToken" class="form-label">Token</label>
            <input type="text" class="form-control" id="signOutInputToken" v-model="tokenSignOut">
          </div>

          <p class="text-black-50 text-break border border-primary p-2" v-if="signOutRes">{{ signOutRes }}</p>

          <div class="mb-3 text-end">
            <button type="button" class="btn btn-secondary" @click="signOut">Sign Out</button>
          </div>
        </form>
      </div>

      <div class="col-6 border-start">
        <h2 class="text-decoration-underline">Todo-List</h2>

        <div style="height: max-content;">

          <form v-if="token">
            <div class="mb-3">
              <label for="todoInputNewTodo" class="form-label">New Todo</label>
              <input type="text" class="form-control" id="todoInputNewTodo" v-model="newTodo">
            </div>

            <div class="mb-3 text-end">
              <button type="button" class="btn btn-secondary" @click="addTodo">Add todo</button>
            </div>

            <hr>

            <div>
              <ul class="list-group list-group-flush" v-for="(todo) in todos" v-bind:key="todo.id">
                <li class="list-group-item">
                  <span class="fs-5">內容：{{ todo.content }} @{{ todo.createTime }}
                    <span :style="!todo.status ? { 'color': 'red' } : { 'color': 'green' }">【{{ todo.status ? "完成" :
                      "未完成" }}】 </span>
                  </span>
                  <input type="text" class="form-control" id="todoInputEditTodo" placeholder="Edit Todo"
                    v-model="todoEdit[todo.id]">
                  <div class="mb-3 text-end">
                    <button type="button" class="btn btn-outline-primary btn-sm"
                      @click="deleteTodo(todo.id)">Delete</button>
                    <button type="button" class="btn btn-outline-primary btn-sm"
                      @click="updateTodo(todo.id)">Update</button>
                    <button type="button" class="btn btn-outline-primary btn-sm"
                      @click="toggleTodoStatus(todo.id)">Toggle
                      Status</button>
                  </div>
                </li>
              </ul>
            </div>
          </form>

          <div v-else>
            <p class="text-black p-2">您尚未登入哦，請先登入並驗證才能使用該功能</p>
          </div>

        </div>

      </div>
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
    // console.log(res);

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
    // console.log(res);
    signOutRes.value = res.data.message;

    tokenCheckout.value = ""
    tokenSignOut.value = ""
    token.value = ""

    document.cookie = `hexschoolTodo=; expires=Thu, 01 Jan 1970 00:00:00 UTC;`;

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

    todos.value.forEach((todo, index) => {
      todos.value[index] = {
        ...todo,
        "createTime": new Date(todo.createTime * 1000).toLocaleString()
      }
    })
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

    await axios.put(`${api}/todos/${todoId}`,
      {
        content: todoEdit.value[todoId]
      }, {
      headers: {
        authorization: token.value
      }
    })
    // console.log(updateTodoRes)

    todoEdit.value[todoId] = ""
    getTodos()

  } catch (error) {
    console.log(error)
  }
}

// 刪除 todo ==============================
const deleteTodo = async (todoId) => {
  try {
    await axios.delete(`${api}/todos/${todoId}`, {
      headers: {
        authorization: token.value
      }
    })
    getTodos()

  } catch (error) {
    console.log(error)
  }
}

// 更新 todo 狀態 ==============================
const toggleTodoStatus = async (todoId) => {
  try {
    await axios.patch(`${api}/todos/${todoId}/toggle`, {},
      {
        headers: {
          authorization: token.value
        }
      })
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

    getTodos
  }
});

</script>

<style scoped></style>