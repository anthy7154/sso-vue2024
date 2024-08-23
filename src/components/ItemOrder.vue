<template>
  <table class="table">
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
  </div>
</template>

<script setup>
import { defineProps } from "vue"

defineProps(['order'])
</script>