<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div class="container">
    <nav class="navbar navbar-light p-4" style="background-color: #e3f2fd;">
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/about">About</RouterLink>
      <RouterLink to="/week1">第一週作業</RouterLink>
      <RouterLink to="/week2">第二週作業</RouterLink>
      <RouterLink to="/week3">第三週作業</RouterLink>
    </nav>
  </div>

  <RouterView />
</template>

<style scoped></style>
