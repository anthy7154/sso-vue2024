<template>
  <div class="list-group" v-for="drink in drinks" :key="drink.id">
    <a href="#" class="list-group-item list-group-item-action" @click.prevent="emit('addCart', drink.id)">
      {{ drink.id }}
      <div class="d-flex w-100 justify-content-between">
        <h5 class="mb-1">{{ drink.name }}</h5>
        <small>${{ drink.price }}</small>
      </div>
      <p class="mb-1">{{ drink.description }}</p>
    </a>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

// ItemMenu vue ====================================
// 取得外部傳入的資料
defineProps(['drinks']);

// 定義事件名稱
const emit = defineEmits(['addCart']);

// 傳遞事件名稱, 參數，執行於 HTML 內
// const emitAddCart = () => {
//   emit('addCart')
// }
// emitAddCart()
// ==================================================
</script>