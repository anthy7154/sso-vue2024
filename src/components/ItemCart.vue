<template>
  <table class="table">
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
        <td><button type="button" class="btn btn-sm" @click="emit('removeCart', item.id)">x</button></td>
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
  </table>
</template>

<script setup>
import { defineProps } from 'vue';

defineProps(['cart'])

const emit = defineEmits(['removeCart'])

</script>