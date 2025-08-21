<script setup>
import { RouterView } from 'vue-router'
import PaymentForm from './components/PaymentForm.vue'
</script>

<template>
  <header>
    <h1>Paga en Ecart Pay!</h1>
  </header>

  <main>
    <PaymentForm />
  </main>

  <RouterView />
</template>

<style scoped>
header {
  text-align: center;
  margin-bottom: 2rem;
  font-family: sans-serif;
}
</style>
