<script setup>
</script>

<template>
  <div>
    <input v-model="sockettotal">
  </div>
  <div>
    <input v-model="strengthreq" placeholder="Str">
    <input v-model="dexterityreq" placeholder="Dex">
    <input v-model="intelligencereq" placeholder="Int">
  </div>
  <div>
    <input v-model="redsockets" placeholder="R">
    <input v-model="greensockets" placeholder="G">
    <input v-model="bluesockets" placeholder="B">
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
